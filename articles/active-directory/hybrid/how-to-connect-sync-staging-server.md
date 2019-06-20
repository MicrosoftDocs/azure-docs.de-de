---
title: 'Azure AD Connect-Synchronisierung: Operative Aufgaben und Überlegungen | Microsoft-Dokumentation'
description: In diesem Thema werden die operativen Aufgaben für Azure AD Connect Sync und die Vorbereitung dieser Komponente für den Betrieb beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176b8509892ef16b631697a686471e7fa52bb380
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381565"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Stagingserver und Notfallwiederherstellung
Mit einem Server im Stagingmodus können Sie Änderungen an der Konfiguration vornehmen und eine Vorschau der Änderungen anzeigen, bevor Sie den Server aktiv schalten. Ein Server im Stagingmodus ermöglicht es Ihnen außerdem, einen vollständigen Import und eine vollständige Synchronisierung durchzuführen und so sicherzustellen, dass alle Änderungen wie erwartet durchgeführt werden, bevor Sie die Änderungen in Ihrer Produktionsumgebung implementieren.

## <a name="staging-mode"></a>Stagingmodus
Der Stagingmodus kann für verschiedene Szenarien genutzt werden, darunter:

* Hochverfügbarkeit.
* Testen und Bereitstellen neuer Konfigurationsänderungen.
* Einführen eines neuen Servers und Außerbetriebnahme des alten Servers.

Bei der Installation können Sie auswählen, dass der Server in den **Stagingmodus**versetzt werden soll. Dadurch wird der Server für Import und Synchronisierung aktiviert, es werden jedoch keine Exportvorgänge ausgeführt. Ein Server im Stagingmodus führt keine Kennwortsynchronisierung und kein Kennwortrückschreiben durch – selbst dann nicht, wenn Sie diese Features bei der Installation ausgewählt haben. Wenn Sie den Stagingmodus deaktivieren, beginnt der Server mit dem Exportieren und aktiviert die Kennwortsynchronisierung und das Kennwortrückschreiben.

> [!NOTE]
> Angenommen Sie, Sie haben eine Azure AD Connect-Instanz, für die Kennworthashsynchronisierung aktiviert ist. Wenn Sie den Stagingmodus aktivieren, beendet der Server das Synchronisieren von Kennwortänderungen aus lokalem AD. Wenn Sie den Stagingmodus deaktivieren, setzt der Server das Synchronisieren von Kennwortänderungen an der Stelle fort, an der das Synchronisieren zuletzt beendet wurde. War der Stagingsmodus längere Zeit für den Server aktiviert, kann es etwas länger dauern, bis der Server alle Kennwortänderungen synchronisiert hat, die es in diesem Zeitraum gegeben hat.
>
>

Sie können einen Export weiterhin mithilfe des Synchronisierungsdienst-Managers erzwingen.

Ein Server im Stagingmodus empfängt weiterhin Änderungen von Active Directory und Azure AD. Er verfügt stets über eine Kopie der aktuellen Änderungen und kann sehr schnell die Funktion eines anderen Servers übernehmen. Wenn Sie Konfigurationsänderungen an Ihrem primären Server vornehmen, müssen Sie die gleichen Änderungen auch für den Server im Stagingmodus durchführen.

Wenn Sie sich bisher nur mit älteren Synchronisierungstechnologien auskennen, müssen Sie wissen, dass Änderungen für den Stagingmodus gelten, seit dieser über eine eigene SQL-Datenbank verfügt. Dank dieser Architektur kann sich der Server im Stagingmodus in einem anderen Datencenter befinden.

### <a name="verify-the-configuration-of-a-server"></a>Überprüfen der Konfiguration eines Servers
Führen Sie zum Anwenden dieser Methode die folgenden Schritte aus:

1. [Vorbereiten](#prepare)
2. [Konfiguration](#configuration)
3. [Importieren und Synchronisieren](#import-and-synchronize)
4. [Überprüfen](#verify)
5. [Wechseln des aktiven Servers](#switch-active-server)

#### <a name="prepare"></a>Vorbereiten
1. Installieren Sie Azure AD Connect, wählen Sie **Stagingmodus** aus, und deaktivieren Sie auf der letzten Seite des Installations-Assistenten die Option **Synchronisierung starten**. In diesem Modus kann das Synchronisierungsmodul manuell ausgeführt werden.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Melden Sie sich ab und wieder an, und wählen Sie im Startmenü die Option **Synchronisierungsdienst**aus.

#### <a name="configuration"></a>Konfiguration
Wenn Sie benutzerdefinierte Änderungen an den primären Server vorgenommen haben und die Konfiguration mit dem Stagingserver vergleichen möchten, verwenden Sie [Azure AD Connect configuration documenter (Azure AD Connect-Konfigurationsdokumentierer)](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importieren und Synchronisieren
1. Wählen Sie **Connectors** aus, und wählen Sie dann den ersten Connector mit dem Typ **Active Directory Domain Services** aus. Klicken Sie auf **Ausführen**, wählen Sie **Vollständiger Import** aus, und klicken Sie auf **OK**. Führen Sie diese Schritte für alle Connectors dieses Typs aus.
2. Wählen Sie den Connector mit dem Typ **Azure Active Directory (Microsoft)** aus. Klicken Sie auf **Ausführen**, wählen Sie **Vollständiger Import** aus, und klicken Sie auf **OK**.
3. Stellen Sie sicher, dass die Registerkarte „Connectors“ noch ausgewählt ist. Klicken Sie für jeden Connector mit dem Typ **Active Directory Domain Services** auf **Ausführen**, wählen Sie **Deltasynchronisierung** aus, und klicken Sie auf **OK**.
4. Wählen Sie den Connector mit dem Typ **Azure Active Directory (Microsoft)** aus. Klicken Sie auf **Ausführen**, wählen Sie **Deltasynchronisierung** aus, und klicken Sie auf **OK**.

Sie haben einen Stagingexport der Änderungen an Azure AD und der lokalen Active Directory-Umgebung durchgeführt (wenn Sie eine Exchange-Hybridbereitstellung verwenden). In den nächsten Schritten können Sie überprüfen, was geändert wird, bevor Sie mit dem Export in die Verzeichnisse beginnen.

#### <a name="verify"></a>Überprüfen
1. Starten Sie eine Eingabeaufforderung, und wechseln Sie zu: `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Führen Sie folgenden Befehl aus: `csexport "Name of Connector" %temp%\export.xml /f:x` Den Namen des Connectors finden Sie im Synchronisierungsdienst. Für Azure AD sieht der Name in etwa wie folgt aus: contoso.com – AAD.
3. Führen Sie folgenden Befehl aus: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Im Ordner „%temp%“ befindet sich nun eine Datei namens „export.csv“, die Sie sich in Microsoft Excel ansehen können. Diese Datei enthält alle Änderungen, die exportiert werden sollen.
4. Nehmen Sie erforderliche Änderungen an den Daten oder der Konfiguration vor, und führen Sie die oben genannten Schritte (Importieren, Synchronisieren, Überprüfen) erneut aus, bis Sie die Änderungen erhalten, die Sie exportieren möchten.

**Grundlegendes zu der Datei „export.csv“** Die Datei ist überwiegend selbsterklärend. Nachfolgend werden zum Verständnis des Inhalts einige Abkürzungen erläutert:
* OMODT: Object Modification Type. Gibt an, ob es sich bei dem Vorgang auf Objektebene um einen Hinzufüge-, Aktualisierungs- oder Löschvorgang handelt.
* AMODT: Attribute Modification Type. Gibt an, ob es sich bei dem Vorgang auf Attributebene um einen Hinzufüge-, Aktualisierungs- oder Löschvorgang handelt.

**Abrufen der allgemeinen Bezeichner** Die Datei „export.csv“ enthält alle Änderungen, die exportiert werden sollen. Jede Zeile entspricht einer Änderung für ein Objekt im Connectorbereich, und das Objekt wird durch das DN-Attribut identifiziert. Das DN-Attribut ist ein eindeutiger Bezeichner, der einem Objekt im Connectorbereich zugewiesen ist. Wenn Sie viele Zeilen/Änderungen in „export.csv“ analysieren müssen, ist es allein anhand des DN-Attributs möglicherweise schwierig, zu ermitteln, auf welche Objekte sich die Änderungen beziehen. Um das Analysieren der Änderungen zu vereinfachen, verwenden Sie das PowerShell-Skript „csanalyzer.ps1“. Das Skript ruft allgemeine Bezeichner der Objekte ab (z.B. „displayName“, „userPrincipalName“). So verwenden Sie das Skript
1. Kopieren Sie das PowerShell-Skript aus dem Abschnitt [CSAnalyzer](#appendix-csanalyzer) in eine Datei mit dem Namen `csanalyzer.ps1`.
2. Öffnen Sie ein PowerShell-Fenster, und navigieren Sie zu dem Ordner, in dem Sie das PowerShell-Skript erstellt haben.
3. Führen Sie `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml` aus.
4. Sie verfügen jetzt über eine Datei namens **processedusers1.csv**, die in Microsoft Excel untersucht werden kann. Beachten Sie, dass die Datei eine Zuordnung des DN-Attributs zum allgemeinen Bezeichner (z.B. „displayName“ und „userPrincipalName“) enthält. Sie enthält derzeit nicht die tatsächlichen Attributänderungen, die exportiert werden sollen.

#### <a name="switch-active-server"></a>Wechseln des aktiven Servers
1. Schalten Sie den derzeit aktiven Server entweder aus (DirSync/FIM/Azure AD Sync), damit kein Export nach Azure AD stattfindet, oder versetzen Sie ihn in den Stagingmodus (Azure AD Connect).
2. Führen Sie auf dem Server im **Stagingmodus** den Installations-Assistenten aus, und deaktivieren Sie den **Stagingmodus**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Notfallwiederherstellung
Teil des Implementierungsentwurfs ist die Planung für einen notfallbedingten Ausfall des Synchronisierungsservers. Es stehen mehrere Modelle zur Verfügung, und die Wahl des Modells ist von verschiedenen Faktoren abhängig:

* Wie hoch ist die Toleranz, wenn während der Ausfallzeit keine Objektänderungen in Azure AD durchgeführt werden können?
* Bei Verwendung der Kennwortsynchronisierung: Ist es für die Benutzer akzeptabel, dass sie in Azure AD ihr altes Kennwort verwenden müssen, wenn sie ihr lokales Kennwort ändern?
* Besteht eine Abhängigkeit von Echtzeitvorgängen, beispielsweise dem Kennwortrückschreiben?

Abhängig von Ihren Antworten auf diese Fragen und Ihrer Organisationsrichtlinie kann eine der folgenden Strategien implementiert werden:

* Führen Sie ggf. eine Neuerstellung durch.
* Richten Sie einen Reserveserver im Standbymodus ein, auch bezeichnet als **Stagingmodus**.
* Verwenden Sie virtuelle Computer.

Wenn Sie nicht die integrierte SQL Express-Datenbank verwenden, lesen Sie sich auch den Abschnitt [Hochverfügbarkeit von SQL](#sql-high-availability) durch.

### <a name="rebuild-when-needed"></a>Neuerstellung, falls erforderlich
Eine sinnvolle Strategie besteht darin, eine ggf. erforderliche Neuerstellung des Servers zu planen. In der Regel sind die Installation des Synchronisierungsmoduls und der anfängliche Import sowie die Synchronisierung innerhalb weniger Stunden abgeschlossen. Wenn kein Ersatzserver verfügbar ist, kann vorübergehend ein Domänencontroller zum Hosten des Synchronisierungsmoduls eingesetzt werden.

Das Synchronisierungsmodul speichert keine Statusinformationen zu den Objekten, deshalb kann die Datenbank mithilfe der Daten in Active Directory und Azure AD neu erstellt werden. Das Attribut **sourceAnchor** wird verwendet, um die lokalen Objekte und die Cloudobjekte miteinander zu verknüpfen. Wenn Sie den Server mit vorhandenen lokalen Objekten und Cloudobjekten neu erstellen, werden diese Objekte bei der Neuinstallation vom Synchronisierungsmodul erneut abgeglichen. Dokumentieren und speichern Sie die am Server vorgenommenen Änderungen, beispielsweise die Filter- und Synchronisierungsregeln. Diese benutzerdefinierten Konfigurationen müssen erneut angewendet werden, bevor Sie die Synchronisierung starten.

### <a name="have-a-spare-standby-server---staging-mode"></a>Stellen Sie einen Reservestandbyserver bereit (Stagingmodus)
Wenn Sie über eine komplexere Umgebung verfügen, wird empfohlen, mindestens einen Standbyserver einzurichten. Bei der Installation können Sie festlegen, dass der Server in den **Stagingmodus**versetzt werden soll.

Weitere Informationen finden Sie unter [Stagingmodus](#staging-mode).

### <a name="use-virtual-machines"></a>Einsatz virtueller Computer
Eine gängige und unterstützte Methode ist die Ausführung des Synchronisierungsmoduls auf einem virtuellen Computer. Wenn ein Problem mit dem Host vorliegt, kann das Image mit dem Synchronisierungsmodulserver zu einem anderen Server migriert werden.

### <a name="sql-high-availability"></a>Hochverfügbarkeit von SQL
Falls Sie nicht SQL Server Express (in Azure AD Connect enthalten) verwenden, sollten Sie auch Hochverfügbarkeit für SQL Server in Erwägung ziehen. Zu den unterstützten Hochverfügbarkeitslösungen gehören SQL-Clustering und SQL AOA (Always On Availability Groups, Always On-Verfügbarkeitsgruppen). Nicht unterstützt wird z.B. die Spiegelung.

Unterstützung für SQL AOA wurde in Version 1.1.524.0 zu Azure AD Connect hinzugefügt. Vor der Installation von Azure AD Connect müssen Sie SQL AOA aktivieren. Während der Installation erkennt Azure AD Connect, ob die bereitgestellte SQL-Instanz für SQL AOA aktiviert ist. Wenn SQL AOA aktiviert ist, prüft Azure AD Connect darüber hinaus, ob SQL AOA für die Verwendung synchroner oder asynchroner Replikationen konfiguriert ist. Bei der Einrichtung des Verfügbarkeitsgruppenlisteners wird empfohlen, die Eigenschaft „RegisterAllProvidersIP“ auf 0 festzulegen. Dies liegt daran, weil Azure AD Connect Verbindungen zu SQL derzeit über den SQL Native Client herstellt und der SQL Native Client die Verwendung der Eigenschaft „MultiSubNetFailover“ nicht unterstützt.

## <a name="appendix-csanalyzer"></a>Anhang CSAnalyzer
Finden Sie im Abschnitt [Überprüfen](#verify) Anleitungen zum Verwenden dieses Skripts.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Nächste Schritte
**Übersichtsthemen**  

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)  
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)  
