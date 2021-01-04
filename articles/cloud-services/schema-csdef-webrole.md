---
title: Azure Cloud Services – Definition WebRole-Schema | Microsoft-Dokumentation
description: Die Azure-Webrolle ist für die Webanwendungsprogrammierung angepasst, die ASP.NET, PHP, WCF und FastCGI unterstützt. Erfahren Sie mehr über die Dienstdefinitionselemente einer Webrolle.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: tgore03
ms.author: tagore
ms.openlocfilehash: 4368bb38a280461fdd77348de60a0e5793ee9582
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011318"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services-Definition – WebRole-Schema
Die Azure-Webrolle ist eine Rolle, die für die Programmierung von Webanwendungen gemäß Unterstützung durch IIS 7 angepasst wird, z.B. ASP.NET, PHP, Windows Communication Foundation und FastCGI.

Die Standarderweiterung für die Dienstdefinitionsdatei lautet „.csdef“.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Grundlegendes Dienstdefinitionsschema für eine Webrolle  
Das Standardformat einer Dienstdefinitionsdatei, die eine Webrolle enthält, lautet wie folgt:

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Schema-Elemente  
Die Dienstdefinitionsdatei enthält die unten angegebenen Elemente, die in den folgenden Abschnitten in diesem Thema ausführlich beschrieben werden:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Einstellung](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Endpunkte](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Zertifikate](#Certificates)

[Certificate](#Certificate)

[Imports](#Imports)

[Importieren](#Import)

[Laufzeit](#Runtime)

[Umgebung](#Environment)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindings](#Bindings)

[Binding](#Binding)

[Startup](#Startup)

[Aufgabe](#Task)

[Contents](#Contents)

[Inhalt](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a>WebRole  
Das `WebRole`-Element beschreibt eine Rolle, die für die Programmierung von Webanwendungen gemäß Unterstützung durch IIS 7 und ASP.NET angepasst ist. Ein Dienst kann null oder mehr Webrollen enthalten.

In der folgenden Tabelle werden die Attribute des Elements `WebRole` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Der Name für die Webrolle. Der Name der Rolle muss eindeutig sein.|  
|enableNativeCodeExecution|boolean|Optional. Der Standardwert ist `true`. Die native Codeausführung und die volle Vertrauenswürdigkeit sind standardmäßig aktiviert. Legen Sie dieses Attribut auf `false` fest, um die native Codeausführung für die Webrolle zu deaktivieren und stattdessen Azure-Teilvertrauenswürdigkeit zu verwenden.|  
|vmsize|Zeichenfolge|Optional. Legen Sie diesen Wert fest, um die Größe des virtuellen Computers zu ändern, der der Rolle zugeordnet ist. Standardwert: `Small`. Weitere Informationen finden Sie unter [Größen virtueller Computer und Azure-Clouddienste](cloud-services-sizes-specs.md).|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>ConfigurationSettings  
Das `ConfigurationSettings`-Element beschreibt die Sammlung mit Konfigurationseinstellungen für eine Webrolle. Dies ist das übergeordnete Element des `Setting`-Elements.

##  <a name="setting"></a><a name="Setting"></a>Setting  
Das `Setting`-Element beschreibt ein Name-Wert-Paar, mit dem eine Konfigurationseinstellung für die Instanz einer Rolle angegeben wird.

In der folgenden Tabelle werden die Attribute des Elements `Setting` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für die Konfigurationseinstellung.|  

Die Konfigurationseinstellungen für eine Rolle umfassen Name-Wert-Paare, die in der Dienstdefinitionsdatei deklariert und in der Dienstkonfigurationsdatei festgelegt werden.

##  <a name="localresources"></a><a name="LocalResources"></a>LocalResources  
Das `LocalResources`-Element beschreibt die Sammlung mit lokalen Speicherressourcen für eine Webrolle. Dies ist das übergeordnete Element des `LocalStorage`-Elements.

##  <a name="localstorage"></a><a name="LocalStorage"></a>LocalStorage  
Mit dem `LocalStorage`-Element wird eine lokale Speicherressource identifiziert, die für den Dienst zur Laufzeit Dateisystem-Speicherplatz bereitstellt. Eine Rolle kann null oder mehr lokale Speicherressourcen definieren.

> [!NOTE]
>  Das `LocalStorage`-Element kann als untergeordnetes Element des `WebRole`-Elements verwendet werden, um die Kompatibilität mit früheren Versionen des Azure SDK zu unterstützen.

In der folgenden Tabelle werden die Attribute des Elements `LocalStorage` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den lokalen Speicher.|  
|cleanOnRoleRecycle|boolean|Optional. Gibt an, ob der lokale Speicher bereinigt werden soll, wenn die Rolle neu gestartet wird. Der Standardwert ist `true`.|  
|sizeInMb|INT|Optional. Die gewünschte Speicherplatzmenge in MB, die für den lokalen Speicher zugeordnet wird. Wenn nichts angegeben ist, wird standardmäßig Speicherplatz mit einer Größe von 100 MB zugeordnet. Die Mindestmenge an Speicherplatz, die zugeordnet werden kann, beträgt 1 MB.<br /><br /> Die maximale Größe der lokalen Ressourcen richtet sich nach der VM-Größe. Weitere Informationen finden Sie unter [Größen virtueller Computer und Azure-Clouddienste](cloud-services-sizes-specs.md).|  
  
Der Name des Verzeichnisses, der der lokalen Speicherressource zugeordnet ist, entspricht dem Wert, der für das Namensattribut angegeben wird.

##  <a name="endpoints"></a><a name="Endpoints"></a>Endpoints  
Das `Endpoints`-Element beschreibt die Sammlung mit den Endpunkten vom Typ „Eingabe (extern)“, „Intern“ und „Instanzeingabe“ einer Rolle. Dies ist das übergeordnete Element der Elemente `InputEndpoint`, `InternalEndpoint` und `InstanceInputEndpoint`.

Eingabe- und interne Endpunkte werden getrennt zugeordnet. Ein Dienst kann insgesamt über 25 Endpunkte vom Typ „Eingabe“, „Intern“ und „Instanzeingabe“ verfügen, die den 25 zulässigen Rollen eines Diensts zugeordnet werden können. Beispielsweise können Sie bei Verwendung von fünf Rollen fünf Eingabeendpunkte pro Rolle, 25 Eingabeendpunkte einer einzelnen Rolle oder jeweils einen Eingabeendpunkt für 25 Rollen zuordnen.

> [!NOTE]
>  Für jede bereitgestellte Rolle ist eine Instanz pro Rolle erforderlich. Die standardmäßige Bereitstellung für ein Abonnement ist auf 20 Kerne und somit auf 20 Instanzen einer Rolle beschränkt. Wenn Ihre Anwendung mehr Instanzen benötigt, als standardmäßig bereitgestellt werden, helfen Ihnen die Informationen zur Erhöhung Ihres Kontingents auf der Supportseite für [Abrechnung, Abonnementverwaltung und Kontingente](https://azure.microsoft.com/support/options/) weiter.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>InputEndpoint  
Das `InputEndpoint`-Element beschreibt einen externen Endpunkt für eine Webrolle.

Sie können mehrere Endpunkte definieren, bei denen es sich um eine Kombination aus HTTP-, HTTPS-, UDP- und TCP-Endpunkten handelt. Sie können eine beliebige Portnummer angeben, die Sie für einen Eingabeendpunkt auswählen, aber die angegebenen Portnummern für die einzelnen Rollen im Dienst müssen eindeutig sein. Wenn Sie beispielsweise angeben, dass eine Webrolle Port 80 für HTTP und Port 443 für HTTPS verwendet, können Sie anschließend ggf. angeben, dass eine zweite Webrolle Port 8080 für HTTP und Port 8043 für HTTPS verwendet.

In der folgenden Tabelle werden die Attribute des Elements `InputEndpoint` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den externen Endpunkt.|  
|Protokoll|Zeichenfolge|Erforderlich. Das Transportprotokoll für den externen Endpunkt. Für eine Webrolle lauten die möglichen Werte `HTTP`, `HTTPS`, `UDP` oder `TCP`.|  
|port|INT|Erforderlich. Der Port für den externen Endpunkt. Sie können eine beliebige Portnummer angeben, die Sie auswählen, aber die angegebenen Portnummern für die einzelnen Rollen im Dienst müssen eindeutig sein.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).|  
|Zertifikat|Zeichenfolge|Für einen HTTPS-Endpunkt erforderlich. Der Name eines Zertifikats, das durch ein `Certificate`-Element definiert wird.|  
|localPort|INT|Optional. Gibt einen Port an, der für interne Verbindungen am Endpunkt verwendet wird. Mit dem `localPort`-Attribut wird der externe Port auf dem Endpunkt einem internen Port auf einer Rolle zugeordnet. Dies ist nützlich, wenn eine Rolle mit einer internen Komponente über einen Port kommunizieren muss, der sich von dem Port unterscheidet, der extern verfügbar gemacht wird.<br /><br /> Wenn nichts angegeben ist, entspricht der Wert von `localPort` dem Wert des `port`-Attributs. Legen Sie den Wert von `localPort` auf „*“ fest, um automatisch einen nicht zugeordneten Port zuzuweisen, der mit der Runtime-API ermittelbar ist.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).<br /><br /> Das `localPort`-Attribut ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.|  
|ignoreRoleInstanceStatus|boolean|Optional. Wenn der Wert dieses Attributs auf `true` festgelegt ist, wird der Status eines Diensts ignoriert, und der Endpunkt wird vom Lastenausgleich nicht entfernt. Das Festlegen dieses Werts auf `true` ist hilfreich, um stark ausgelastete Instanzen eines Diensts zu debuggen. Standardwert: `false`. **Hinweis:**  Ein Endpunkt kann auch dann weiterhin Datenverkehr empfangen, wenn die Rolle nicht den Status „Bereit“ aufweist.|  
|loadBalancerProbe|Zeichenfolge|Optional. Der Name des Lastenausgleichstests, der dem Eingabeendpunkt zugeordnet ist. Weitere Informationen finden Sie unter [LoadBalancerProbe-Schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>InternalEndpoint  
Das `InternalEndpoint`-Element beschreibt einen internen Endpunkt für eine Webrolle. Eine interner Endpunkt ist nur für andere Rolleninstanzen verfügbar, die innerhalb des Diensts ausgeführt werden. Er ist nicht für Clients außerhalb des Diensts verfügbar. Webrollen, die das `Sites`-Element nicht enthalten, können nur über einen einzelnen internen HTTP-, UDP- oder TCP-Endpunkt verfügen.

In der folgenden Tabelle werden die Attribute des Elements `InternalEndpoint` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den internen Endpunkt.|  
|Protokoll|Zeichenfolge|Erforderlich. Das Transportprotokoll für den internen Endpunkt. Mögliche Werte sind `HTTP`, `TCP`, `UDP` oder `ANY`.<br /><br /> Mit dem Wert `ANY` wird angegeben, dass alle Protokolle und alle Ports zulässig sind.|  
|port|INT|Optional. Der Port, der für interne Verbindungen mit Lastenausgleich auf dem Endpunkt verwendet wird. Für einen Endpunkt mit Lastenausgleich werden zwei Ports verwendet. Der Port für die öffentliche IP-Adresse und der Port für die private IP-Adresse. Normalerweise wird hierfür der gleiche Port verwendet, aber Sie können auch unterschiedliche Ports verwenden.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).<br /><br /> Das `Port`-Attribut ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
Das `InstanceInputEndpoint`-Element beschreibt einen Instanz-Eingabeendpunkt für eine Webrolle. Ein Instanz-Eingabeendpunkt wird einer bestimmten Rolleninstanz zugeordnet, indem die Portweiterleitung im Lastenausgleich verwendet wird. Jeder Instanz-Eingabeendpunkt ist einem bestimmten Port aus einem Bereich möglicher Ports zugeordnet. Dies ist das übergeordnete Element des `AllocatePublicPortFrom`-Elements.

Das `InstanceInputEndpoint`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.7 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `InstanceInputEndpoint` beschrieben.
  
| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den Endpunkt.|  
|localPort|INT|Erforderlich. Gibt den internen Port an, auf den alle Rolleninstanzen lauschen, um eingehenden Datenverkehr zu empfangen, der vom Lastenausgleich weitergeleitet wird. Mögliche Werte liegen zwischen 1 und 65.535 (einschließlich).|  
|Protokoll|Zeichenfolge|Erforderlich. Das Transportprotokoll für den internen Endpunkt. Mögliche Werte sind `udp` oder `tcp`. Verwenden Sie `tcp` für HTTP/HTTPS-basierten Datenverkehr.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom  
Das `AllocatePublicPortFrom`-Element beschreibt den öffentlichen Portbereich, der von externen Kunden verwendet werden kann, um auf die einzelnen Instanz-Eingabeendpunkte zuzugreifen. Die öffentliche Portnummer (VIP) wird aus diesem Bereich zugeordnet und während der Bereitstellung und Aktualisierung des Mandanten den individuellen Rolleninstanzendpunkten zugewiesen. Dies ist das übergeordnete Element des `FixedPortRange`-Elements.

Das `AllocatePublicPortFrom`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.7 oder höher verwendet wird.

##  <a name="fixedport"></a><a name="FixedPort"></a>FixedPort  
Das `FixedPort`-Element gibt den Port für den internen Endpunkt an, um auf dem Endpunkt die Verwendung von Verbindungen mit Lastenausgleich zu ermöglichen.

Das `FixedPort`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `FixedPort` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|port|INT|Erforderlich. Der Port für den internen Endpunkt. Dies hat die gleiche Wirkung wie das Festlegen des Minimums und Maximums von `FixedPortRange` auf denselben Port.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange  
Das `FixedPortRange`-Element gibt den Bereich der Ports an, die dem internen Endpunkt oder Instanz-Eingabeendpunkt zugewiesen sind, und legt den Port fest, der für Verbindungen mit Lastenausgleich auf dem Endpunkt verwendet wird.

> [!NOTE]
>  Das `FixedPortRange`-Element verhält sich je nach dem Element, auf dem es sich befindet, auf unterschiedliche Weise. Wenn sich das `FixedPortRange`-Element unter dem `InternalEndpoint`-Element befindet, werden alle Ports auf dem Lastenausgleich innerhalb des Bereichs der min- und max-Attribute für alle virtuellen Computer geöffnet, auf denen die Rolle ausgeführt wird. Wenn sich das `FixedPortRange`-Element auf dem `InstanceInputEndpoint`-Element befindet, wird nur ein Port innerhalb des Bereichs der min- und max-Attribute auf jedem virtuellen Computer geöffnet, auf dem die Rolle ausgeführt wird.

Das `FixedPortRange`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `FixedPortRange` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|Min|INT|Erforderlich. Der niedrigste Port im Bereich. Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).|  
|max|Zeichenfolge|Erforderlich. Der höchste Port im Bereich. Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).|  

##  <a name="certificates"></a><a name="Certificates"></a>Certificates  
Das `Certificates`-Element beschreibt die Sammlung mit Zertifikaten für eine Webrolle. Dies ist das übergeordnete Element des `Certificate`-Elements. Eine Rolle kann über beliebig viele zugeordnete Zertifikate verfügen. Weitere Informationen zur Verwendung des Certificate-Elements finden Sie unter [Ändern der Definitions- und Konfigurationsdateien für den Dienst](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certificate  
Das `Certificate`-Element beschreibt ein Zertifikat, das einer Webrolle zugeordnet ist.

In der folgenden Tabelle werden die Attribute des Elements `Certificate` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Ein Name für dieses Zertifikat, mit dem darauf verwiesen wird, wenn es einem `InputEndpoint`-HTTPS-Element zugeordnet ist.|  
|storeLocation|Zeichenfolge|Erforderlich. Der Speicherort des Zertifikatspeichers, in dem sich dieses Zertifikat auf dem lokalen Computer befinden kann. Mögliche Werte sind `CurrentUser` und `LocalMachine`.|  
|storeName|Zeichenfolge|Erforderlich. Der Name des Zertifikatspeichers, in dem sich dieses Zertifikat auf dem lokalen Computer befindet. Mögliche Werte sind die Namen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot` und `AddressBook` von integrierten Speichern oder der Name eines beliebigen benutzerdefinierten Speichers. Wenn der Name eines benutzerdefinierten Speichers angegeben wird, wird der Speicher automatisch erstellt.|  
|permissionLevel|Zeichenfolge|Optional. Gibt die Zugriffsberechtigungen an, die für die Rollenprozesse gewährt werden. Geben Sie die Berechtigung `elevated` an, wenn nur Prozesse mit erhöhten Rechten auf den privaten Schlüssel zugreifen sollen. Mit der Berechtigung `limitedOrElevated` können alle Rollenprozesse auf den privaten Schlüssel zugreifen. Mögliche Werte sind `limitedOrElevated` oder `elevated`. Standardwert: `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a>Imports  
Das `Imports`-Element beschreibt eine Sammlung mit Importmodulen für eine Webrolle, mit denen Komponenten dem Gastbetriebssystem hinzugefügt werden. Dies ist das übergeordnete Element des `Import`-Elements. Dieses Element ist optional, und eine Rolle kann nur einen Importblock aufweisen. 

Das `Imports`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

##  <a name="import"></a><a name="Import"></a>Import  
Das `Import`-Element gibt ein Modul zum Hinzufügen zum Gastbetriebssystem an.

Das `Import`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `Import` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|moduleName|Zeichenfolge|Erforderlich. Der Name des zu importierenden Moduls. Gültige Importmodule sind:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> Mit den Modulen RemoteAccess und RemoteForwarder können Sie Ihre Rolleninstanz für Remotedesktopverbindungen konfigurieren. Weitere Informationen finden Sie unter [Aktivieren einer Remotedesktopverbindung](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Das Modul Diagnostics ermöglicht Ihnen das Sammeln von Diagnosedaten für eine Rolleninstanz.|  

##  <a name="runtime"></a><a name="Runtime"></a>Runtime  
Das `Runtime`-Element beschreibt eine Sammlung mit Umgebungsvariableneinstellungen für eine Webrolle, mit denen die Laufzeitumgebung des Azure-Hostprozesses gesteuert wird. Dies ist das übergeordnete Element des `Environment`-Elements. Dieses Element ist optional, und eine Rolle kann nur einen Laufzeitblock haben.

Das `Runtime`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle sind die Attribute des `Runtime`-Elements beschrieben:  

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|executionContext|Zeichenfolge|Optional. Gibt den Kontext an, in dem der Rollenprozess gestartet wird. Der Standardkontext ist `limited`.<br /><br /> -   `limited`: Der Prozess wird ohne Administratorrechte gestartet.<br />-   `elevated`: Der Prozess wird mit Administratorrechten gestartet.|  

##  <a name="environment"></a><a name="Environment"></a>Environment  
Das `Environment`-Element beschreibt eine Sammlung mit Umgebungsvariableneinstellungen für eine Webrolle. Dies ist das übergeordnete Element des `Variable`-Elements. Für eine Rolle kann eine beliebige Anzahl von Umgebungsvariablen festgelegt werden.

##  <a name="variable"></a><a name="Variable"></a>Variable  
Das `Variable`-Element gibt eine Umgebungsvariable zum Festlegen im Gastbetriebssystem an.

Das `Variable`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle sind die Attribute des `Variable`-Elements beschrieben:  

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Der Name der festzulegenden Umgebungsvariablen.|  
|value|Zeichenfolge|Optional. Der Wert, der für die Umgebungsvariable festgelegt werden soll. Sie müssen entweder ein Wertattribut oder ein `RoleInstanceValue`-Element einbinden.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>RoleInstanceValue  
Das `RoleInstanceValue`-Element gibt den xPath an, über den der Wert der Variablen abgerufen wird.

In der folgenden Tabelle werden die Attribute des Elements `RoleInstanceValue` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|xpath|Zeichenfolge|Optional. Pfad zum Speicherort der Bereitstellungseinstellungen für die Instanz. Weitere Informationen finden Sie unter [Verfügbarmachen von Rollenkonfigurationseinstellungen als Umgebungsvariable mit XPath](cloud-services-role-config-xpath.md).<br /><br /> Sie müssen entweder ein Wertattribut oder ein `RoleInstanceValue`-Element einbinden.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a>EntryPoint  
Das `EntryPoint`-Element gibt den Einstiegspunkt für eine Rolle an. Dies ist das übergeordnete Element des `NetFxEntryPoint`-Elements. Diese Elemente ermöglichen Ihnen das Angeben einer anderen Anwendung als der Standarddatei „WaWorkerHost.exe“ als Rolleneinstiegspunkt.

Das `EntryPoint`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint  
Das `NetFxEntryPoint`-Element gibt das Programm an, das für eine Rolle ausgeführt werden soll.

> [!NOTE]
>  Das `NetFxEntryPoint`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `NetFxEntryPoint` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|assemblyName|Zeichenfolge|Erforderlich. Der Pfad und Dateiname der Assembly, die den Einstiegspunkt enthält. Der Pfad ist relativ zum Ordner **\\%ROLEROOT%\Approot**. (Geben Sie **\\%ROLEROOT%\Approot** in `commandLine` nicht an, da dies bereits vorausgesetzt wird.) **%ROLEROOT%** ist eine von Azure verwaltete Umgebungsvariable, die den Speicherort des Stammordners für Ihre Rolle darstellt. Der Ordner **\\%ROLEROOT%\Approot** stellt den Anwendungsordner für Ihre Rolle dar.<br /><br /> Für HWC-Rollen ist der Pfad immer relativ zum Ordner **\\%ROLEROOT%\Approot\bin**.<br /><br /> Für Webrollen der IIS-Vollversion und von IIS Express wird wie folgt vorgegangen: Wenn die Assembly relativ zum Ordner **\\%ROLEROOT%\Approot** nicht gefunden werden kann, wird **\\%ROLEROOT%\Approot\bin** durchsucht.<br /><br /> Dieses Fallback-Verhalten für die IIS-Vollversion ist keine bewährte Methode und wird in zukünftigen Versionen unter Umständen nicht mehr möglich sein.|  
|targetFrameworkVersion|Zeichenfolge|Erforderlich. Die .NET Framework-Version, unter der die Assembly erstellt wurde. Beispiel: `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a>Sites  
Das `Sites`-Element beschreibt eine Sammlung mit Websites und Webanwendungen, die in einer Webrolle gehostet werden. Dies ist das übergeordnete Element des `Site`-Elements. Wenn Sie kein `Sites`-Element angeben,wird Ihre Webrolle als Legacywebrolle gehostet, und Sie können unter Ihrer Webrolle nur eine Website hosten. Dieses Element ist optional, und eine Rolle kann nur einen Websiteblock haben.

Das `Sites`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

##  <a name="site"></a><a name="Site"></a>Site  
Das `Site`-Element gibt eine Website oder Webanwendung an, die Teil der Webrolle ist.

Das `Site`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `Site` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Der Name der Website oder Anwendung.|  
|physicalDirectory|Zeichenfolge|Der Speicherort des Inhaltsverzeichnisses für das Stammverzeichnis der Site. Der Speicherort kann als absoluter Pfad oder relativ zum CSDEF-Speicherort angegeben werden.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a>VirtualApplication  
Das `VirtualApplication`-Element definiert eine Anwendung in Internetinformationsdienste (IIS) 7. Es handelt sich um eine Gruppierung von Dateien, mit denen Inhalte oder über Protokolle wie HTTP Dienste bereitgestellt werden. Wenn Sie eine Anwendung in IIS 7 erstellen, wird der Pfad der Anwendung Teil der Site-URL.

Das `VirtualApplication`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `VirtualApplication` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Gibt einen Namen zur Identifizierung der virtuellen Anwendung an.|  
|physicalDirectory|Zeichenfolge|Erforderlich. Gibt den Pfad auf dem Entwicklungscomputer an, der die virtuelle Anwendung enthält. Im Serveremulator ist IIS so konfiguriert, dass Inhalt von diesem Speicherort abgerufen wird. Beim Bereitstellen in Azure wird der Inhalt des physischen Verzeichnisses zusammen mit dem Rest des Diensts verpackt. Wenn das Dienstpaket in Azure bereitgestellt wird, wird IIS mit dem Speicherort des entpackten Inhalts konfiguriert.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a>VirtualDirectory  
Das `VirtualDirectory`-Element gibt einen Verzeichnisnamen an (auch als Pfad bezeichnet), den Sie in IIS angeben und einem physischen Verzeichnis auf einem lokalen Server oder Remoteserver zuordnen.

Das `VirtualDirectory`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `VirtualDirectory` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Gibt einen Namen zur Identifizierung des virtuellen Verzeichnisses an.|  
|value|physicalDirectory|Erforderlich. Gibt den Pfad auf dem Entwicklungscomputer an, der die Inhalte der Website bzw. des virtuellen Verzeichnisses enthält. Im Serveremulator ist IIS so konfiguriert, dass Inhalt von diesem Speicherort abgerufen wird. Beim Bereitstellen in Azure wird der Inhalt des physischen Verzeichnisses zusammen mit dem Rest des Diensts verpackt. Wenn das Dienstpaket in Azure bereitgestellt wird, wird IIS mit dem Speicherort des entpackten Inhalts konfiguriert.|  

##  <a name="bindings"></a><a name="Bindings"></a>Bindings  
Das `Bindings`-Element beschreibt eine Sammlung mit Bindungen für eine Website. Dies ist das übergeordnete Element des `Binding`-Elements. Das Element ist für jedes `Site`-Element erforderlich. Weitere Informationen zum Konfigurieren von Endpunkten finden Sie unter [Ermöglichen der Kommunikation für Rolleninstanzen in Azure](cloud-services-enable-communication-role-instances.md).

Das `Bindings`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

##  <a name="binding"></a><a name="Binding"></a>Binding  
Das `Binding`-Element gibt Konfigurationsinformationen an, die für Anforderungen zum Kommunizieren mit einer Website oder Webanwendung erforderlich sind.

Das `Binding`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|name|Zeichenfolge|Erforderlich. Gibt einen Namen zur Identifizierung der Bindung an.|  
|endpointName|Zeichenfolge|Erforderlich. Gibt den Endpunktnamen an, für den die Bindung erfolgen soll.|  
|hostHeader|Zeichenfolge|Optional. Gibt einen Hostnamen an, mit dem Sie mehrere Sites mit unterschiedlichen Hostnamen unter einer einzelnen Kombination aus IP-Adresse und Portnummer hosten können.|  

##  <a name="startup"></a><a name="Startup"></a>Startup  
Das `Startup`-Element beschreibt eine Sammlung mit Aufgaben, die ausgeführt werden, wenn die Rolle gestartet wird. Dies kann das übergeordnete Element des `Variable`-Elements sein. Weitere Informationen zur Verwendung der Rollenstartaufgaben finden Sie unter [Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst](cloud-services-startup-tasks.md). Dieses Element ist optional, und eine Rolle kann nur einen Startblock haben.

In der folgenden Tabelle sind die Attribute des `Startup`-Elements beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|priority|INT|Nur zur internen Verwendung.|  

##  <a name="task"></a><a name="Task"></a>Task  
Das `Task`-Element gibt Startaufgaben an, die ausgeführt werden, wenn die Rolle gestartet wird. Startaufgaben können verwendet werden, um Aufgaben durchzuführen, mit denen die Rolle darauf vorbereitet wird, diese Komponenten zur Softwareinstallation oder andere Anwendungen auszuführen. Aufgaben werden in der Reihenfolge ausgeführt, in der sie im `Startup`-Elementblock angezeigt werden.

Das `Task`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `Task` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|commandLine|Zeichenfolge|Erforderlich. Ein Skript, z.B. eine CMD-Datei, das die auszuführenden Befehle enthält. Startbefehls- und Batchdateien müssen im ANSI-Format gespeichert werden. Dateiformate, bei denen am Anfang der Datei eine Bytereihenfolge-Marke festgelegt wird, werden nicht richtig verarbeitet.|  
|executionContext|Zeichenfolge|Gibt den Kontext an, in dem das Skript ausgeführt wird.<br /><br /> -   `limited` [Standard]: Die Ausführung erfolgt mit den gleichen Rechten wie für die Rolle, die den Prozess hostet.<br />-   `elevated`: Die Ausführung erfolgt mit Administratorrechten.|  
|taskType|Zeichenfolge|Gibt das Ausführungsverhalten des Befehls an.<br /><br /> -   `simple` [Standard]: Das System wartet, bis die Aufgabe beendet wird (exit), bevor andere Aufgaben gestartet werden.<br />-   `background`: Das System wartet nicht auf die Beendigung der Aufgabe.<br />-   `foreground`: Ähnelt „background“, aber die Rolle wird erst neu gestartet, nachdem alle Aufgaben im Vordergrund beendet wurden.|  

##  <a name="contents"></a><a name="Contents"></a>Contents  
Das `Contents`-Element beschreibt die Sammlung mit dem Inhalt einer Webrolle. Dies ist das übergeordnete Element des `Content`-Elements.

Das `Contents`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

##  <a name="content"></a><a name="Content"></a>Content  
Das `Content`-Element definiert den Quellspeicherort des Inhalts, der auf den virtuellen Azure-Computer kopiert werden soll, und den Zielpfad für den Kopiervorgang.

Das `Content`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `Content` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|destination|Zeichenfolge|Erforderlich. Der Speicherort des virtuellen Azure-Computers, auf dem der Inhalt angeordnet wird. Dieser Speicherort ist relativ zum Ordner **%ROLEROOT%\Approot**.|  

Dies ist das übergeordnete Element des `SourceDirectory`-Elements.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>SourceDirectory  
Das `SourceDirectory`-Element definiert das lokale Verzeichnis, aus dem der Inhalt kopiert wird. Verwenden Sie dieses Element, um die lokalen Inhalte anzugeben, die auf den virtuellen Azure-Computer kopiert werden sollen.

Das `SourceDirectory`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `SourceDirectory` beschrieben.

| attribute | type | BESCHREIBUNG |  
| --------- | ---- | ----------- |  
|path|Zeichenfolge|Erforderlich. Der relative oder absolute Pfad eines lokalen Verzeichnisses, dessen Inhalte auf den virtuellen Azure-Computer kopiert werden sollen. Die Erweiterung von Umgebungsvariablen im Verzeichnispfad wird unterstützt.|  
  
## <a name="see-also"></a>Weitere Informationen
[Clouddienst-Definitionsschema (klassisch)](schema-csdef-file.md)




