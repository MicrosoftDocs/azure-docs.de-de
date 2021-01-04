---
title: Azure Cloud Services – Definition WorkerRole-Schema | Microsoft-Dokumentation
description: Die Azure-Workerrolle wird für allgemeine Entwicklungsaufgaben verwendet und kann Hintergrundverarbeitung für eine Webrolle durchführen. Erfahren Sie mehr zum Schema von Azure-Workerrollen.
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: tgore03
ms.author: tagore
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005162"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services-Definition – WorkerRole-Schema
Die Azure-Workerrolle ist eine Rolle, die für die generalisierte Entwicklung nützlich ist und die Hintergrundverarbeitung für eine Webrolle durchführen kann.

Die Standarderweiterung für die Dienstdefinitionsdatei lautet „.csdef“.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Grundlegendes Dienstdefinitionsschema für eine Workerrolle
Das Standardformat einer Dienstdefinitionsdatei, die eine Workerrolle enthält, lautet wie folgt:

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
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
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-Elemente
Die Dienstdefinitionsdatei enthält die unten angegebenen Elemente, die in den folgenden Abschnitten in diesem Thema ausführlich beschrieben werden:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Einstellung](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Endpunkte](#Endpoints)

[InputEndpoint](#InputEndpoint)

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

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Startup](#Startup)

[Aufgabe](#Task)

[Contents](#Contents)

[Inhalt](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a>WorkerRole
Das `WorkerRole`-Element beschreibt eine Rolle, die für die generalisierte Entwicklung nützlich ist und die Hintergrundverarbeitung für eine Webrolle durchführen kann. Ein Dienst kann null oder mehr Workerrollen enthalten.

In der folgenden Tabelle werden die Attribute des Elements `WorkerRole` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|name|Zeichenfolge|Erforderlich. Der Name für die Workerrolle. Der Name der Rolle muss eindeutig sein.|
|enableNativeCodeExecution|boolean|Optional. Der Standardwert ist `true`. Die native Codeausführung und die volle Vertrauenswürdigkeit sind standardmäßig aktiviert. Legen Sie dieses Attribut auf `false` fest, um die native Codeausführung für die Workerrolle zu deaktivieren und stattdessen Azure-Teilvertrauenswürdigkeit zu verwenden.|
|vmsize|Zeichenfolge|Optional. Legen Sie diesen Wert fest, um die Größe des virtuellen Computers zu ändern, der dieser Rolle zugeordnet ist. Standardwert: `Small`. Eine Liste mit den möglichen VM-Größen und den dazugehörigen Attributen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>ConfigurationSettings
Das `ConfigurationSettings`-Element beschreibt die Sammlung mit Konfigurationseinstellungen für eine Workerrolle. Dies ist das übergeordnete Element des `Setting`-Elements.

##  <a name="setting"></a><a name="Setting"></a>Setting
Das `Setting`-Element beschreibt ein Name-Wert-Paar, mit dem eine Konfigurationseinstellung für die Instanz einer Rolle angegeben wird.

In der folgenden Tabelle werden die Attribute des Elements `Setting` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für die Konfigurationseinstellung.|

Die Konfigurationseinstellungen für eine Rolle umfassen Name-Wert-Paare, die in der Dienstdefinitionsdatei deklariert und in der Dienstkonfigurationsdatei festgelegt werden.

##  <a name="localresources"></a><a name="LocalResources"></a>LocalResources
Das `LocalResources`-Element beschreibt die Sammlung von lokalen Speicherressourcen für eine Workerrolle. Dies ist das übergeordnete Element des `LocalStorage`-Elements.

##  <a name="localstorage"></a><a name="LocalStorage"></a>LocalStorage
Mit dem `LocalStorage`-Element wird eine lokale Speicherressource identifiziert, die für den Dienst zur Laufzeit Dateisystem-Speicherplatz bereitstellt. Eine Rolle kann null oder mehr lokale Speicherressourcen definieren.

> [!NOTE]
>  Das `LocalStorage`-Element kann als untergeordnetes Element des `WorkerRole`-Elements verwendet werden, um die Kompatibilität mit früheren Versionen des Azure SDK zu unterstützen.

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
Das `InputEndpoint`-Element beschreibt einen externen Endpunkt für eine Workerrolle.

Sie können mehrere Endpunkte definieren, bei denen es sich um eine Kombination aus HTTP-, HTTPS-, UDP- und TCP-Endpunkten handelt. Sie können eine beliebige Portnummer angeben, die Sie für einen Eingabeendpunkt auswählen, aber die angegebenen Portnummern für die einzelnen Rollen im Dienst müssen eindeutig sein. Wenn Sie beispielsweise angeben, dass eine Workerrolle Port 80 für HTTP und Port 443 für HTTPS verwendet, können Sie anschließend ggf. angeben, dass eine zweite Workerrolle Port 8080 für HTTP und Port 8043 für HTTPS verwendet.

In der folgenden Tabelle werden die Attribute des Elements `InputEndpoint` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den externen Endpunkt.|
|Protokoll|Zeichenfolge|Erforderlich. Das Transportprotokoll für den externen Endpunkt. Für eine Workerrolle lauten die möglichen Werte `HTTP`, `HTTPS`, `UDP` oder `TCP`.|
|port|INT|Erforderlich. Der Port für den externen Endpunkt. Sie können eine beliebige Portnummer angeben, die Sie auswählen, aber die angegebenen Portnummern für die einzelnen Rollen im Dienst müssen eindeutig sein.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).|
|Zertifikat|Zeichenfolge|Für einen HTTPS-Endpunkt erforderlich. Der Name eines Zertifikats, das durch ein `Certificate`-Element definiert wird.|
|localPort|INT|Optional. Gibt einen Port an, der für interne Verbindungen am Endpunkt verwendet wird. Mit dem `localPort`-Attribut wird der externe Port auf dem Endpunkt einem internen Port auf einer Rolle zugeordnet. Dies ist nützlich, wenn eine Rolle mit einer internen Komponente über einen Port kommunizieren muss, der sich von dem Port unterscheidet, der extern verfügbar gemacht wird.<br /><br /> Wenn nichts angegeben ist, entspricht der Wert von `localPort` dem Wert des `port`-Attributs. Legen Sie den Wert von `localPort` auf „*“ fest, um automatisch einen nicht zugeordneten Port zuzuweisen, der mit der Runtime-API ermittelbar ist.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).<br /><br /> Das `localPort`-Attribut ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.|
|ignoreRoleInstanceStatus|boolean|Optional. Wenn der Wert dieses Attributs auf `true` festgelegt ist, wird der Status eines Diensts ignoriert, und der Endpunkt wird vom Lastenausgleich nicht entfernt. Das Festlegen dieses Werts auf `true` ist hilfreich, um stark ausgelastete Instanzen eines Diensts zu debuggen. Standardwert: `false`. **Hinweis:** Ein Endpunkt kann auch dann weiterhin Datenverkehr empfangen, wenn die Rolle nicht den Status „Bereit“ aufweist.|
|loadBalancerProbe|Zeichenfolge|Optional. Der Name des Lastenausgleichstests, der dem Eingabeendpunkt zugeordnet ist. Weitere Informationen finden Sie unter [LoadBalancerProbe-Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>InternalEndpoint
Das `InternalEndpoint`-Element beschreibt einen internen Endpunkt für eine Workerrolle. Eine interner Endpunkt ist nur für andere Rolleninstanzen verfügbar, die innerhalb des Diensts ausgeführt werden. Er ist nicht für Clients außerhalb des Diensts verfügbar. Eine Workerrolle kann bis zu fünf interne HTTP-, UDP- oder TCP-Endpunkte haben.

In der folgenden Tabelle werden die Attribute des Elements `InternalEndpoint` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|name|Zeichenfolge|Erforderlich. Ein eindeutiger Name für den internen Endpunkt.|
|Protokoll|Zeichenfolge|Erforderlich. Das Transportprotokoll für den internen Endpunkt. Mögliche Werte sind `HTTP`, `TCP`, `UDP` oder `ANY`.<br /><br /> Mit dem Wert `ANY` wird angegeben, dass alle Protokolle und alle Ports zulässig sind.|
|port|INT|Optional. Der Port, der für interne Verbindungen mit Lastenausgleich auf dem Endpunkt verwendet wird. Für einen Endpunkt mit Lastenausgleich werden zwei Ports verwendet. Der Port für die öffentliche IP-Adresse und der Port für die private IP-Adresse. Normalerweise wird hierfür der gleiche Port verwendet, aber Sie können auch unterschiedliche Ports verwenden.<br /><br /> Mögliche Werte reichen von 1 bis 65.535 einschließlich (Azure SDK-Version 1.7 oder höher).<br /><br /> Das `Port`-Attribut ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Das `InstanceInputEndpoint`-Element beschreibt einen Instanz-Eingabeendpunkt für eine Workerrolle. Ein Instanz-Eingabeendpunkt wird einer bestimmten Rolleninstanz zugeordnet, indem die Portweiterleitung im Lastenausgleich verwendet wird. Jeder Instanz-Eingabeendpunkt ist einem bestimmten Port aus einem Bereich möglicher Ports zugeordnet. Dies ist das übergeordnete Element des `AllocatePublicPortFrom`-Elements.

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
Das `Certificates`-Element beschreibt die Sammlung mit Zertifikaten für eine Workerrolle. Dies ist das übergeordnete Element des `Certificate`-Elements. Eine Rolle kann über beliebig viele zugeordnete Zertifikate verfügen. Weitere Informationen zur Verwendung des Certificate-Elements finden Sie unter [Ändern der Definitions- und Konfigurationsdateien für den Dienst](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certificate
Das `Certificate`-Element beschreibt ein Zertifikat, das einer Workerrolle zugeordnet ist.

In der folgenden Tabelle werden die Attribute des Elements `Certificate` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|name|Zeichenfolge|Erforderlich. Ein Name für dieses Zertifikat, mit dem darauf verwiesen wird, wenn es einem `InputEndpoint`-HTTPS-Element zugeordnet ist.|
|storeLocation|Zeichenfolge|Erforderlich. Der Speicherort des Zertifikatspeichers, in dem sich dieses Zertifikat auf dem lokalen Computer befinden kann. Mögliche Werte sind `CurrentUser` und `LocalMachine`.|
|storeName|Zeichenfolge|Erforderlich. Der Name des Zertifikatspeichers, in dem sich dieses Zertifikat auf dem lokalen Computer befindet. Mögliche Werte sind die Namen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot` und `AddressBook` von integrierten Speichern oder der Name eines beliebigen benutzerdefinierten Speichers. Wenn der Name eines benutzerdefinierten Speichers angegeben wird, wird der Speicher automatisch erstellt.|
|permissionLevel|Zeichenfolge|Optional. Gibt die Zugriffsberechtigungen an, die für die Rollenprozesse gewährt werden. Geben Sie die Berechtigung `elevated` an, wenn nur Prozesse mit erhöhten Rechten auf den privaten Schlüssel zugreifen sollen. Mit der Berechtigung `limitedOrElevated` können alle Rollenprozesse auf den privaten Schlüssel zugreifen. Mögliche Werte sind `limitedOrElevated` oder `elevated`. Standardwert: `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Imports
Das `Imports`-Element beschreibt eine Sammlung mit Importmodulen für eine Workerrolle, mit denen Komponenten dem Gastbetriebssystem hinzugefügt werden. Dies ist das übergeordnete Element des `Import`-Elements. Dieses Element ist optional, und eine Rolle kann nur einen Laufzeitblock haben.

Das `Imports`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

##  <a name="import"></a><a name="Import"></a>Import
Das `Import`-Element gibt ein Modul zum Hinzufügen zum Gastbetriebssystem an.

Das `Import`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `Import` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|moduleName|Zeichenfolge|Erforderlich. Der Name des zu importierenden Moduls. Gültige Importmodule sind:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> Mit den Modulen RemoteAccess und RemoteForwarder können Sie Ihre Rolleninstanz für Remotedesktopverbindungen konfigurieren. Weitere Informationen finden Sie unter [Aktivieren einer Remotedesktopverbindung](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Das Modul Diagnostics ermöglicht Ihnen das Sammeln von Diagnosedaten für eine Rolleninstanz.|

##  <a name="runtime"></a><a name="Runtime"></a>Runtime
Das `Runtime`-Element beschreibt eine Sammlung mit Umgebungsvariableneinstellungen für eine Workerrolle, mit denen die Laufzeitumgebung des Azure-Hostprozesses gesteuert wird. Dies ist das übergeordnete Element des `Environment`-Elements. Dieses Element ist optional, und eine Rolle kann nur einen Laufzeitblock haben.

Das `Runtime`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.3 oder höher verwendet wird.

In der folgenden Tabelle sind die Attribute des `Runtime`-Elements beschrieben:

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|executionContext|Zeichenfolge|Optional. Gibt den Kontext an, in dem der Rollenprozess gestartet wird. Der Standardkontext ist `limited`.<br /><br /> -   `limited`: Der Prozess wird ohne Administratorrechte gestartet.<br />-   `elevated`: Der Prozess wird mit Administratorrechten gestartet.|

##  <a name="environment"></a><a name="Environment"></a>Environment
Das `Environment`-Element beschreibt eine Sammlung mit Umgebungsvariableneinstellungen für eine Workerrolle. Dies ist das übergeordnete Element des `Variable`-Elements. Für eine Rolle kann eine beliebige Anzahl von Umgebungsvariablen festgelegt werden.

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
|assemblyName|Zeichenfolge|Erforderlich. Der Pfad und Dateiname der Assembly, die den Einstiegspunkt enthält. Der Pfad ist relativ zum Ordner **\\%ROLEROOT%\Approot**. (Geben Sie **\\%ROLEROOT%\Approot** in `commandLine` nicht an, da dies bereits vorausgesetzt wird.) **%ROLEROOT%** ist eine von Azure verwaltete Umgebungsvariable, die den Speicherort des Stammordners für Ihre Rolle darstellt. Der Ordner **\\%ROLEROOT%\Approot** stellt den Anwendungsordner für Ihre Rolle dar.|
|targetFrameworkVersion|Zeichenfolge|Erforderlich. Die .NET Framework-Version, unter der die Assembly erstellt wurde. Beispiel: `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>ProgramEntryPoint
Das `ProgramEntryPoint`-Element gibt das Programm an, das für eine Rolle ausgeführt werden soll. Mit dem `ProgramEntryPoint`-Element können Sie einen Programmeinstiegspunkt angeben, der nicht auf einer .NET-Assembly basiert.

> [!NOTE]
>  Das `ProgramEntryPoint`-Element ist nur verfügbar, wenn die Azure SDK-Version 1.5 oder höher verwendet wird.

In der folgenden Tabelle werden die Attribute des Elements `ProgramEntryPoint` beschrieben.

| attribute | type | BESCHREIBUNG |
| --------- | ---- | ----------- |
|commandLine|Zeichenfolge|Erforderlich. Der Pfad, Dateiname und alle Befehlszeilenargumente des auszuführenden Programms. Der Pfad ist relativ zum Ordner **%ROLEROOT%\Approot**. (Geben Sie **%ROLEROOT%\Approot** in commandLine nicht an, da dies bereits vorausgesetzt wird.) **%ROLEROOT%** ist eine von Azure verwaltete Umgebungsvariable, die den Speicherort des Stammordners für Ihre Rolle darstellt. Der Ordner **%ROLEROOT%\Approot** stellt den Anwendungsordner für Ihre Rolle dar.<br /><br /> Wenn das Programm endet, wird die Rolle recycelt. Im Allgemeinen sollten Sie das Programm also so festlegen, dass es weiter ausgeführt und nicht nur zum Durchführen einer begrenzten Aufgabe gestartet wird.|
|setReadyOnProcessStart|boolean|Erforderlich. Gibt an, ob die Rolleninstanz darauf wartet, dass vom Befehlszeilenprogramm das Signal zum erfolgten Start bereitgestellt wird. Dieser Wert muss hier auf `true` festgelegt werden. Das Festlegen des Werts auf `false` ist für die spätere Nutzung reserviert.|

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
Das `Contents`-Element beschreibt die Sammlung mit dem Inhalt einer Workerrolle. Dies ist das übergeordnete Element des `Content`-Elements.

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



