---
title: Problembehandlung bei der SSIS Integration Runtime-Verwaltung
description: Dieser Artikel enthält eine Anleitung zur Fehlerbehebung bei Problemen mit der Verwaltung von SSIS Integration Runtime (SSIS IR).
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361841"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Problembehandlung bei der SSIS Integration Runtime-Verwaltung in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dieser Artikel enthält eine Anleitung zur Problembehandlung bei Verwaltungsproblemen in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), auch bezeichnet als SSIS IR.

## <a name="overview"></a>Übersicht

Wenn beim Bereitstellen oder Aufheben der Bereitstellung von SSIS IR Probleme auftreten, wird eine Fehlermeldung im Microsoft Azure Data Factory-Portal oder ein von einem PowerShell-Cmdlet zurückgegebener Fehler angezeigt. Der Fehler wird immer im Format eines Fehlercodes mit einer detaillierten Fehlermeldung angezeigt.

Bei dem Fehlercode „InternalServerError“ hat der Dienst vorübergehende Probleme, und Sie sollten den Vorgang später wiederholen. Wenden Sie sich an das Azure Data Factory-Supportteam, wenn auch bei der Wiederholung des Vorgangs Probleme auftreten.

Andernfalls können drei wichtige externe Abhängigkeiten Fehler verursachen: Azure SQL-Datenbank oder eine verwaltete Azure SQL-Instanz, ein benutzerdefiniertes Setupskript und eine Konfiguration von virtuellen Netzwerken.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Probleme bei SQL-Datenbank oder verwalteter SQL-Instanz

SQL-Datenbank oder eine verwaltete SQL-Datenbank-Instanz ist erforderlich, wenn Sie SSIS IR mit einer SSIS-Katalogdatenbank bereitstellen. Die SSIS IR muss auf SQL-Datenbank oder die verwaltete SQL-Instanz zugreifen können. Außerdem muss das Anmeldekonto für SQL-Datenbank oder die verwaltete SQL-Instanz über die Berechtigung zum Erstellen einer SSIS-Katalogdatenbank (SSISDB) verfügen. Wenn ein Fehler auftritt, wird im Data Factory-Portal ein Fehlercode mit einer detaillierten SQL-Ausnahmemeldung angezeigt. Anhand der Informationen in der folgenden Aufstellung können Sie die Fehlercodes beheben.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Dieses Problem tritt möglicherweise auf, wenn Sie eine neue SSIS IR bereitstellen oder wenn IR ausgeführt wird. Wenn dieser Fehler während der IR-Bereitstellung auftritt, wird in der Fehlermeldung möglicherweise eine detaillierte SqlException-Meldung angezeigt, die eines der folgenden Probleme angibt:

* Ein Problem mit der Netzwerkverbindung. Überprüfen Sie, ob der Hostname für SQL-Datenbank oder die verwaltete SQL-Instanz verfügbar ist. Vergewissern Sie sich außerdem, dass der SSIS IR-Zugriff auf den Server nicht durch eine Firewall oder eine Netzwerksicherheitsgruppe (NSG) blockiert wird.
* Während der SQL-Authentifizierung ist bei der Anmeldung ein Fehler aufgetreten. Das angegebene Konto kann sich nicht bei der SQL Server-Datenbank anmelden. Achten Sie darauf, das richtige Benutzerkonto anzugeben.
* Während der Microsoft Azure Active Directory-Authentifizierung (Azure AD) (verwaltete Identität) ist bei der Anmeldung ein Fehler aufgetreten. Fügen Sie einer AAD-Gruppe die verwaltete Identität Ihrer Factory hinzu, und stellen Sie sicher, dass die verwaltete Identität Zugriffsberechtigungen auf den Katalogdatenbankserver hat.
* Verbindungstimeout. Dieser Fehler wird immer durch eine sicherheitsbezogene Konfiguration verursacht. Wir empfehlen Folgendes:
  1. Erstellen Sie einen neuen virtuellen Computer.
  1. Binden Sie den virtuellen Computer in das gleiche virtuelle Microsoft Azure-Netzwerk wie IR ein, sofern sich IR in einem virtuellen Netzwerk befindet.
  1. Installieren Sie SSMS, und überprüfen Sie den Status von SQL-Datenbank oder der verwalteten SQL-Instanz.

Beheben Sie bei anderen Problemen das entsprechende in der detaillierten SqlException-Meldung angezeigte Problem. Wenn weiterhin Probleme auftreten, wenden Sie sich an das Supportteam für SQL-Datenbank oder die verwaltete SQL-Instanz.

Wenn der Fehler während der Ausführung von IR angezeigt wird, verhindern Änderungen an der Netzwerksicherheitsgruppe oder der Firewall wahrscheinlich, dass der SSIS IR-Workerknoten auf SQL-Datenbank oder die verwaltete SQL-Instanz zugreifen kann. Entsperren Sie den SSIS IR-Workerknoten, damit er auf Azure-Datenbank oder die verwaltete SQL-Instanz zugreifen kann.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Diese Art von Fehlermeldung kann wie folgt aussehen: „Die Datenbank ‚SSISDB‘ hat ihr Limit erreicht. Partitionieren oder löschen Sie Daten, löschen Sie Indizes, oder informieren Sie sich in der Dokumentation über mögliche Lösungen.“ 

Lösungsvorschläge:
* Erhöhen Sie die Kontingentgröße der SSISDB-Datenbank.
* Ändern Sie die Konfiguration von SSISDB, um die Größe folgendermaßen zu reduzieren:
   * Reduzieren des Aufbewahrungszeitraums und der Anzahl von Projektversionen
   * Verkürzung des Aufbewahrungszeitraums des Protokolls.
   * Ändern der Standardebene des Protokolls.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Dieser Fehler bedeutet, dass SQL-Datenbank oder die verwaltete SQL-Instanz bereits über eine SSISDB-Datenbank verfügt und von einer anderen IR verwendet wird. Sie müssen entweder eine andere SQL-Datenbank-Instanz oder eine andere verwaltete SQL-Instanz bereitstellen oder aber die vorhandene SSISDB löschen und die neue IR neu starten.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Dieser Fehler kann aus einem der folgenden Gründe auftreten:

* Das Benutzerkonto, das für die SSIS IR konfiguriert ist, hat keine Berechtigung zum Erstellen der Datenbank. Sie können dem Benutzer die Berechtigung zum Erstellen der Datenbank erteilen.
* Beim Erstellen der Datenbank tritt ein Timeout auf, z. B. ein Ausführungstimeout oder ein Timeout bei Datenbankvorgängen. Wiederholen Sie den Vorgang zu einem späteren Zeitpunkt. Wenn diese Wiederholung nicht funktioniert, wenden Sie sich an das Supportteam für SQL-Datenbank oder die verwaltete SQL-Instanz.

Überprüfen Sie bei anderen Problemen die SqlException-Fehlermeldung, und beheben Sie das entsprechende in den Fehlerdetails aufgeführte Problem. Wenn weiterhin Probleme auftreten, wenden Sie sich an das Supportteam für SQL-Datenbank oder die verwaltete SQL-Instanz.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Diese Art von Fehlermeldung sieht wie folgt aus: „Ungültiger Objektname ‚catalog.catalog_properties‘.“ In diesem Fall verfügen Sie entweder bereits über eine Datenbank mit dem Namen „SSISDB“, die jedoch nicht mit SSIS IR erstellt wurde, oder die Datenbank befindet sich in einem ungültigen Zustand, der durch Fehler in der letzten SSIS IR-Bereitstellung verursacht wurde. Sie können die vorhandene Datenbank „SSISDB“ löschen oder aber eine neue SQL-Datenbank-Instanz oder verwaltete SQL-Instanz für die IR konfigurieren.

## <a name="custom-setup-issues"></a>Probleme beim benutzerdefinierten Setup

Das benutzerdefinierte Setup bietet eine Schnittstelle, um eigene Setupschritte während der Bereitstellung oder Neukonfiguration der SSIS IR hinzuzufügen. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

Stellen Sie sicher, dass Ihr Container nur die notwendigen benutzerdefinierten Setupdateien enthält, da alle Dateien im Container auf den SSIS IR-Workerknoten heruntergeladen werden. Es wird empfohlen, das benutzerdefinierte Setupskript auf einem lokalen Computer zu testen, um Probleme bei der Skriptausführung zu beheben, bevor Sie das Skript in SSIS IR ausführen.

Der Container des benutzerdefinierten Setupskripts wird während der IR-Ausführung überprüft, da SSIS IR regelmäßig aktualisiert wird. Diese Aktualisierung erfordert Zugriff auf den Container, damit das benutzerdefinierte Setupskript heruntergeladen und erneut installiert werden kann. Bei diesem Vorgang wird außerdem überprüft, ob auf den Container zugegriffen werden kann und ob die Datei „main.cmd“ vorhanden ist.

Bei allen Fehlern, die das benutzerdefinierte Setup betreffen, wird ein CustomSetupScriptFailure-Fehlercode mit einem untergeordneten Fehlercode wie beispielsweise CustomSetupScriptBlobContainerInaccessible oder CustomSetupScriptNotFound angezeigt.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Dieser Fehler bedeutet, dass SSIS IR nicht auf den Azure-Blobcontainer für das benutzerdefinierte Setup zugreifen kann. Vergewissern Sie sich, dass der SAS-URI des Containers erreichbar und nicht abgelaufen ist.

Beenden Sie die ausgeführte IR, konfigurieren Sie die IR mit dem SAS-URI des Containers für das neue benutzerdefinierte Setup neu, und starten Sie dann die IR neu.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Dieser Fehler bedeutet, dass SSIS IR kein benutzerdefiniertes Setupskript („main.cmd“) in Ihrem Blobcontainer finden kann. Stellen Sie sicher, dass „main.cmd“ im Container vorhanden ist, da dies der Einstiegspunkt für die Installation des benutzerdefinierten Setups ist.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Dieser Fehler bedeutet, dass bei der Ausführung des benutzerdefinierten Setupskripts („main.cmd“) Fehler aufgetreten sind. Testen Sie das Skript zunächst auf dem lokalen Computer, oder überprüfen Sie die Ausführungsprotokolle des benutzerdefinierten Setups in Ihrem Blobcontainer.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Dieser Fehler gibt ein Timeout bei der Ausführung des benutzerdefinierten Setupskripts an. Stellen Sie sicher, dass Ihr Skript unbeaufsichtigt ausgeführt werden kann und keine interaktive Eingabe erforderlich ist. Vergewissern Sie sich auch, dass Ihr Blobcontainer nur die erforderlichen Dateien für das benutzerdefinierte Setup enthält. Wir empfehlen Ihnen, das Skript zuerst auf dem lokalen Computer zu testen. Sie sollten auch die Ausführungsprotokolle für das benutzerdefinierte Setup im Blobcontainer überprüfen. Der maximale Zeitraum für das benutzerdefinierte Setup ist auf 45 Minuten vor der Zeitüberschreitung festgelegt. Der maximale Zeitraum umfasst die Zeit, in der alle Dateien aus dem Container heruntergeladen und in SSIS IR installiert werden. Wenn ein längerer Zeitraum erforderlich ist, erstellen Sie ein Supportticket.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Dieser Fehler bedeutet, dass beim Hochladen der Ausführungsprotokolle für das benutzerdefinierte Setup in den Blobcontainer Fehler aufgetreten sind. Dieses Problem tritt auf, weil SSIS IR nicht über Schreibberechtigungen für den Blobcontainer verfügt, oder aufgrund von Speicher- oder Netzwerkproblemen. Wenn das benutzerdefinierte Setup erfolgreich ausgeführt wird, hat dieser Fehler keine Auswirkungen auf SSIS-Funktionen, aber die Protokolle fehlen. Wenn bei dem benutzerdefinierten Setup ein anderer Fehler auftritt und das Protokoll nicht hochgeladen wird, wird dieser Fehler zuerst gemeldet, sodass das Protokoll zur Analyse hochgeladen werden kann. Nachdem dieses Problem behoben wurde, werden außerdem alle spezifischeren Probleme gemeldet. Wenn dieses Problem nach einem erneuten Versuch nicht behoben wurde, wenden Sie sich an das Azure Data Factory-Supportteam.

## <a name="virtual-network-configuration"></a>Konfiguration von virtuellen Netzwerken

Wenn Sie SSIS IR in Azure Virtual Network einbinden, verwendet SSIS IR das virtuelle Netzwerk, das unter dem Benutzerabonnement vorhanden ist. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).
Wenn nach dem Start der SSIS-IR Probleme mit der Netzwerkverbindung auftreten, können Sie versuchen, das Problem mithilfe des [Verbindungsdiagnosetools](ssis-integration-runtime-diagnose-connectivity-faq.md) zu diagnostizieren.
Wenn ein auf ein virtuelles Netzwerk bezogenes Problem auftritt, wird einer der folgenden Fehler angezeigt.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Dieser Fehler kann aus verschiedenen Gründen auftreten. Informationen zur Problembehandlung finden Sie in den Abschnitten [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) und [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Verboten

Diese Art von Fehlermeldung kann wie folgt aussehen: „SubnetId ist für das aktuelle Konto nicht aktiviert. Der Microsoft.Batch-Ressourcenanbieter ist nicht unter dem gleichen Abonnement wie das VNET registriert.“

Diese Details bedeuten, dass Azure Batch nicht auf Ihr virtuelles Netzwerk zugreifen kann. Registrieren Sie den Microsoft.Batch-Ressourcenanbieter unter demselben Abonnement wie das virtuelle Netzwerk.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Diese Art von Fehlermeldung kann einer der folgenden ähneln: 

- „Entweder ist das angegebene VNET nicht vorhanden, oder der Batch-Dienst hat keinen Zugriff darauf.“
- „Das angegebene Subnetz xxx besteht nicht.“

Diese Fehler bedeuten, dass das virtuelle Netzwerk nicht vorhanden ist, der Azure Batch-Dienst nicht darauf zugreifen kann, oder das angegebene Subnetz nicht vorhanden ist. Stellen Sie sicher, dass das virtuelle Netzwerk und das Subnetz vorhanden sind und dass Azure Batch darauf zugreifen kann.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Diese Art von Fehlermeldung kann wie folgt aussehen: „Integration Runtime in VNET konnte nicht bereitgestellt werden. Wenn der DNS-Server oder die NSG-Einstellungen konfiguriert sind, vergewissern Sie sich, dass der DNS-Server zugänglich ist und NSG ordnungsgemäß konfiguriert ist.“

In diesem Fall verwenden Sie wahrscheinlich eine benutzerdefinierte Konfiguration des DNS-Servers oder der NSG-Einstellungen, was dazu führt, dass der für SSIS IR erforderliche Name des Azure-Servers nicht aufgelöst werden kann oder nicht darauf zugegriffen werden kann. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md). Wenn weiterhin Probleme auftreten, wenden Sie sich an das Azure Data Factory-Supportteam.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR wird regelmäßig automatisch aktualisiert. Bei der Aktualisierung wird ein neuer Azure Batch-Pool erstellt und der alte Azure Batch-Pool gelöscht. Außerdem werden die auf ein virtuelles Netzwerk bezogenen Ressourcen für den alten Pool gelöscht und die neuen auf ein virtuelles Netzwerk bezogenen Ressourcen unter Ihrem Abonnement erstellt. Dieser Fehler bedeutet, dass beim Löschen von auf ein virtuelles Netzwerk bezogenen Ressourcen für den alten Pool aufgrund einer Löschsperre auf Abonnement- oder Ressourcengruppenebene Fehler aufgetreten sind. Da der Kunde die Löschsperre steuert und festlegt, muss er die Löschsperre in diesem Fall entfernen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Wenn bei der SSIS IR-Bereitstellung Fehler auftreten, werden alle erstellten Ressourcen gelöscht. Wenn jedoch eine Sperre für das Löschen von Ressourcen auf Abonnement- oder Ressourcengruppenebene vorhanden ist, werden die Virtual Network-Ressourcen nicht wie erwartet gelöscht. Entfernen Sie die Löschsperre, und starten Sie die IR neu, um diesen Fehler zu beheben.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

Wenn Sie SSIS IR beenden, werden alle auf Virtual Network bezogenen Ressourcen gelöscht. Beim Löschen können jedoch Fehler auftreten, wenn eine Sperre für das Löschen von Ressourcen auf Abonnement- oder Ressourcengruppenebene vorhanden ist. Auch hier wird die Löschsperre vom Kunden gesteuert und festgelegt. Daher muss der Kunde die Löschsperre entfernen und dann SSIS IR erneut beenden.

### <a name="nodeunavailable"></a>NodeUnavailable

Dieser Fehler tritt bei der Ausführung der IR auf und gibt an, dass die IR fehlerhaft ist. Dieser Fehler wird immer durch eine Änderung in der Konfiguration des DNS-Servers oder der Netzwerksicherheitsgruppe verursacht, die verhindert, dass SSIS IR eine Verbindung mit einem erforderlichen Dienst herstellen kann. Da die Konfiguration des DNS-Servers und der Netzwerksicherheitsgruppe vom Kunden gesteuert wird, muss der Kunde die entsprechenden Probleme beheben. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md). Wenn weiterhin Probleme auftreten, wenden Sie sich an das Azure Data Factory-Supportteam.

## <a name="static-public-ip-addresses-configuration"></a>Konfiguration der statischen öffentlichen IP-Adressen

Wenn Sie die Azure-SSIS IR mit Azure Virtual Network verknüpfen, können Sie auch Ihre eigenen statischen öffentlichen IP-Adressen für die IR verwenden. Dann kann die IR auf Datenquellen zugreifen, die den Zugriff auf bestimmte IP-Adressen einschränken. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).

Neben den oben genannten Problemen beim virtuellen Netzwerk können auch Probleme im Zusammenhang mit statischen öffentlichen IP-Adressen auftreten. Überprüfen Sie die folgenden Fehler, um Hilfe zu erhalten.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Dieser Fehler kann beim Starten der Azure-SSIS IR aus verschiedenen Gründen auftreten:

| Fehlermeldung | Lösung|
|:--- |:--- |
| The provided static public IP address is already used, please provide two unused ones for your Azure-SSIS Integration Runtime. (Die angegebene statische öffentliche IP-Adresse wird bereits verwendet. Geben Sie zwei nicht verwendete für Ihre Azure-SSIS Integration Runtime an.) | Sie sollten zwei nicht verwendete statische öffentliche IP-Adressen auswählen oder aktuelle Verweise auf die angegebene öffentliche IP-Adresse entfernen und dann die Azure-SSIS IR neu starten. |
| The provided static public IP address has no DNS name, please provide two of them with DNS name for your Azure-SSIS Integration Runtime. (Die angegebene statische öffentliche IP-Adresse hat keinen DNS-Namen. Geben Sie zwei davon mit DNS-Name für Ihre Azure-SSIS Integration Runtime an.) | Sie können den DNS-Namen der öffentlichen IP-Adresse im Azure-Portal einrichten, wie in der folgenden Abbildung gezeigt wird. Führen Sie dazu die folgenden Schritte aus: (1) Öffnen Sie das Azure-Portal, und navigieren Sie zur Ressourcenseite dieser öffentlichen IP-Adresse. (2) Wählen Sie den Abschnitt **Konfiguration** aus, und richten Sie den DNS-Namen ein. Klicken Sie dann auf die Schaltfläche **Speichern**; (3) Starten Sie Ihre Azure-SSIS IR neu. |
| Die angegebenen VNET-Adressen und statischen öffentlichen IP-Adressen für Ihre Azure-SSIS Integration Runtime müssen sich an demselben Standort befinden. | Entsprechend den Anforderungen des Azure-Netzwerks sollten sich die statische öffentliche IP-Adresse und das virtuelle Netzwerk an demselben Standort und in demselben Abonnement befinden. Geben Sie zwei gültige statische öffentliche IP-Adressen an, und starten Sie die Azure-SSIS IR neu. |
| Die angegebene statische öffentliche IP-Adresse ist ein grundlegender Wert. Geben Sie zwei Standardwerte für Ihre Azure-SSIS Integration Runtime an. | Hilfe finden Sie unter [SKUs der öffentlichen IP-Adresse](../virtual-network/public-ip-addresses.md#sku). |

![Azure-SSIS-Integrationslaufzeit](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Wenn bei der Azure-SSIS IR-Bereitstellung Fehler auftreten, werden alle erstellten Ressourcen gelöscht. Gibt es jedoch eine Sperre für das Löschen von Ressourcen auf Abonnementebene oder Ebene der Ressourcengruppe (die Ihre statische öffentliche IP-Adresse enthält), werden die Netzwerkressourcen nicht wie erwartet gelöscht. Entfernen Sie zum Beheben des Fehlers die Löschsperre, und starten Sie die IR neu.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Wenn Sie die Azure-SSIS IR beenden, werden alle Netzwerkressourcen gelöscht, die in der Ressourcengruppe mit Ihrer öffentlichen IP-Adresse erstellt wurden. Das Löschen kann jedoch fehlschlagen, wenn es eine Sperre für das Löschen von Ressourcen auf Abonnementebene oder Ebene der Ressourcengruppe gibt (die Ihre statische öffentliche IP-Adresse enthält). Entfernen Sie die Löschsperre, und starten Sie die IR neu.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Die Azure-SSIS IR wird regelmäßig automatisch aktualisiert. Während des Upgrades werden neue IR-Knoten erstellt, und die alten Knoten werden gelöscht. Außerdem werden die erstellten Netzwerkressourcen (z.B. der Lastenausgleich und die Netzwerksicherheitsgruppe) für die alten Knoten gelöscht; die neuen Netzwerkressourcen werden unter Ihrem Abonnement erstellt. Dieser Fehler bedeutet, dass das Löschen der Netzwerkressourcen für die alten Knoten aufgrund einer Löschsperre bei dem Abonnement oder der Ressourcengruppe (die Ihre statische öffentliche IP-Adresse enthält) fehlgeschlagen ist. Entfernen Sie die Löschsperre, damit wir die alten Knoten bereinigen und deren statische öffentliche IP-Adresse freigeben können. Andernfalls kann die statische öffentliche IP-Adresse nicht freigegeben werden, und wir können Ihre IR nicht weiter aktualisieren.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen verwenden möchten, sollten zwei öffentliche IP-Adressen angegeben werden. Eine wird verwendet, um die IR-Knoten sofort zu erstellen, und die andere wird während des Upgrades der IR verwendet. Dieser Fehler kann auftreten, wenn die andere öffentliche IP-Adresse während des Upgrades nicht verwendet werden kann. Informationen zu möglichen Ursachen finden Sie unter [InvalidPublicIPSpecified](#InvalidPublicIPSpecified).