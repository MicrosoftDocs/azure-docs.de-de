---
title: SAP LaMa-Connector für Azure | Microsoft-Dokumentation
description: Verwalten von SAP-Systemen in Azure mithilfe von SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: e3f541e28f47bb6456b441811d23baa9e020fde7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959151"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-Connector für Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Allgemeine Supportanweisung: Bitte öffnen Sie einen SAP-Incident immer in der Komponente BC-VCM-LVM-HYPERV, wenn Sie Support für SAP LaMa oder den Azure-Connector benötigen.

Viele Kunden verwenden SAP LaMa, um ihre SAP-Landschaft auszuführen und zu überwachen. Seit SAP LaMa 3.0 SP05 beinhaltet das Tool standardmäßig einen Connector für die Verwendung in Azure. Mit diesem Connector können Sie VMs freigeben und starten, verwaltete Datenträger kopieren und verschieben sowie verwaltete Datenträger löschen. Mit diesen grundlegenden Vorgängen können Sie SAP-Systeme mit SAP LaMa verschieben, kopieren, klonen und aktualisieren.

Dieser Leitfaden beschreibt, wie Sie den Azure-Connector für SAP LaMa einrichten, VMs erstellen, mit denen adaptive SAP-Systeme installiert werden können, und diese konfigurieren.

> [!NOTE]
> Der Connector ist nur in der SAP LaMa Enterprise Edition verfügbar.

## <a name="resources"></a>Ressourcen

Die folgenden SAP-Hinweise beziehen sich auf das Thema SAP LaMa in Azure:

| Hinweisnummer | Titel |
| --- | --- |
| [2343511] |Microsoft Azure-Connector für SAP Landscape Management (LaMa) |
| [2350235] |SAP Landscape Management 3.0 – Enterprise Edition |

Weitere Informationen finden Sie im auch [SAP-Hilfeportal für SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Allgemeine Hinweise

* Aktivieren Sie unter Setup -> Einstellungen -> Engine *Automatische Bereitstellungspunkterstellung*.  
  Wenn SAP LaMa Volumes über die SAP Adaptive Extensions auf einer VM bereitstellt, muss der Bereitstellungspunkt vorhanden sein, wenn diese Einstellung nicht aktiviert ist.

* Verwenden Sie ein separates Subnetz und keine dynamischen IP-Adressen, um das „Stehlen“ von IP-Adressen zu verhindern, wenn Sie neue VMs bereitstellen, und SAP-Instanzen nicht vorbereitet sind.  
  Wenn Sie die dynamische IP-Adresszuordnung im Subnetz verwenden, das auch von SAP LaMa genutzt wird, kann die Vorbereitung eines SAP-Systems mit SAP LaMa fehlschlagen. Wenn ein SAP-System nicht vorbereitet ist, sind die IP-Adressen nicht reserviert. Das heißt, sie werden möglicherweise anderen VMs zugeordnet.

* Wenn Sie sich bei verwalteten Hosts anmelden, stellen Sie sicher, dass die Bereitstellung von Dateisystemen weiterhin aufgehoben werden kann.  
  Wenn Sie sich auf einer Linux-VM anmelden und das Arbeitsverzeichnis in ein Verzeichnis in einem Bereitstellungspunkt ändern, z.B. „/usr/sap/AH1/ASCS00/exe“, kann die Bereitstellung des Volumes nicht aufgehoben werden, und das Verschieben oder Aufheben der Vorbereitung schlägt fehl.

* Achten Sie darauf, CLOUD_NETCONFIG_MANAGE auf virtuellen Linux-Computern mit SLES zu deaktivieren. Weitere Informationen finden Sie in der SUSE-Knowledge Base unter [7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Einrichten eines Azure-Connectors für SAP LaMa

Der Azure-Connector ist seit SAP LaMa 3.0 SP05 im Lieferumfang des Tools inbegriffen. Es wird empfohlen, immer das neueste Unterstützungspaket und den neuesten Patch für SAP LaMa 3.0 zu installieren.

Der Azure-Connector nutzt die Azure Resource Manager-API zum Verwalten Ihrer Azure-Ressourcen. SAP LaMa kann sich über einen Dienstprinzipal oder eine verwaltete Identität bei dieser API authentifizieren. Wenn Ihre SAP LaMa-Instanz auf einem virtuellen Azure-Computer ausgeführt wird, empfehlen wir die Verwendung einer verwalteten Identität, wie im Kapitel [Verwenden einer verwalteten Identität zum Erhalten von Zugriff auf die Azure-API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) beschrieben. Wenn Sie einen Dienstprinzipal verwenden möchten, befolgen Sie die Schritte im Kapitel [Verwenden eines Dienstprinzipals zum Erhalten von Zugriff auf die Azure-API](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Verwenden eines Dienstprinzipals zum Erhalten von Zugriff auf die Azure-API

Der Azure-Connector kann einen Dienstprinzipal für die Autorisierung bei Microsoft Azure verwenden. Sie können folgendermaßen einen Dienstprinzipal für SAP Landscape Management (LaMa) erstellen.

1. Besuchen Sie https://portal.azure.com.
1. Öffnen Sie das Blatt „Azure Active Directory“.
1. Klicken Sie auf „App-Registrierungen“.
1. Klicken Sie auf „Neue Registrierung“.
1. Geben Sie einen Namen ein, und klicken Sie auf „Registrieren“.
1. Wählen Sie die neue App aus, und klicken Sie auf der Registerkarte „Einstellungen“ auf „Zertifikate & Geheimnisse“.
1. Erstellen Sie einen neuen geheimen Clientschlüssel, geben Sie eine Beschreibung für einen neuen Schlüssel ein, wählen Sie aus, wann das Geheimnis ablaufen soll, und klicken Sie auf „Speichern“.
1. Notieren Sie sich den Wert. Er dient als Kennwort für den Dienstprinzipal.
1. Notieren Sie sich die Anwendungs-ID. Sie dient als Benutzername für den Dienstprinzipal.

Der Dienstprinzipal hat standardmäßig keine Zugriffsberechtigungen für Ihre Azure-Ressourcen. Sie müssen dem Dienstprinzipal Zugriffsberechtigungen dafür gewähren.

1. Besuchen Sie https://portal.azure.com.
1. Öffnen Sie das Blatt „Ressourcengruppen“.
1. Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten.
1. Klicken Sie auf „Zugriffssteuerung (IAM)“.
1. Klicken Sie auf „Rollenzuweisung hinzufügen“.
1. Wählen Sie die Rolle „Mitwirkender“ aus.
1. Geben Sie den Namen der Anwendung ein, die Sie zuvor erstellt haben.
1. Klicken Sie auf Speichern.
1. Wiederholen Sie die Schritte 3 bis 8 für alle Ressourcengruppen, die Sie in SAP LaMa verwenden möchten.

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Verwenden einer verwalteten Identität zum Erhalten von Zugriff auf die Azure-API

Um eine verwaltete Identität verwenden zu können, muss Ihre SAP LaMa-Instanz auf einer Azure-VM ausgeführt werden, die über eine vom System oder Benutzer zugewiesene Identität verfügt. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md) und [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

Die verwaltete Identität hat standardmäßig keine Zugriffsberechtigungen für Ihre Azure-Ressourcen. Sie müssen ihr Berechtigungen für den Zugriff darauf erteilen.

1. Besuchen Sie https://portal.azure.com.
1. Öffnen Sie das Blatt „Ressourcengruppen“.
1. Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten.
1. Klicken Sie auf „Zugriffssteuerung (IAM)“.
1. Klicken Sie auf „Hinzufügen“ > „Rollenzuweisung hinzufügen“.
1. Wählen Sie die Rolle „Mitwirkender“ aus.
1. Wählen Sie für „Zugriff zuweisen zu“ die Option „Virtueller Computer“ aus.
1. Wählen Sie den virtuellen Computer aus, auf dem Ihre SAP LaMa-Instanz ausgeführt wird.
1. Klicken Sie auf Speichern.
1. Wiederholen Sie die Schritte für alle Ressourcengruppen, die Sie in SAP LaMa verwenden möchten.

Wählen Sie in Ihrer Azure-Connectorkonfiguration für SAP LaMa die Option „Verwaltete Identität verwenden“ aus, um die Verwendung der verwalteten Identität zu aktivieren. Wenn Sie eine vom System zugewiesene Identität verwenden möchten, muss das Feld „Benutzername“ leer bleiben. Wenn Sie eine von einem Benutzer zugewiesene Identität verwenden möchten, geben Sie die ID der vom Benutzer zugewiesenen Identität im Feld „Benutzername“ ein.

### <a name="create-a-new-connector-in-sap-lama"></a>Erstellen eines neuen Connectors in SAP LaMa

Öffnen Sie die SAP LaMa-Website, und navigieren Sie zu „Infrastructure“ (Infrastruktur). Wechseln Sie zur Registerkarte „Cloud Managers“ (Cloud-Manager), und klicken Sie auf „Add“ (Hinzufügen). Wählen Sie den Microsoft Azure-Cloudadapter aus, und klicken Sie auf „Next“ (Weiter). Geben Sie Folgendes ein:

* Label (Bezeichnung): Wählen Sie einen Namen für die Connectorinstanz aus.
* User Name (Benutzername): Die Anwendungs-ID des Dienstprinzipals oder die ID der vom Benutzer zugewiesenen Identität des virtuellen Computers. Weitere Informationen finden Sie unter [Verwenden einer system- oder benutzerseitig zugewiesenen Identität].
* Password (Kennwort): Schlüssel/Kennwort des Dienstprinzipals. Sie können dieses Feld leer lassen, wenn Sie eine vom System oder vom Benutzer zugewiesene Identität verwenden.
* URL: Behalten Sie die Standardeinstellung `https://management.azure.com/` bei.
* Monitoring Interval (Sekunden) (Überwachungsintervall (Sekunden)): Sollte mindestens 300 sein
* Verwaltete Identität verwenden: SAP LaMa kann für die Authentifizierung bei der Azure-API eine vom System oder vom Benutzer zugewiesene Identität verwenden. Weitere Informationen finden Sie im Kapitel [Verwenden einer verwalteten Identität zum Erhalten von Zugriff auf die Azure-API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) in diesem Handbuch.
* Subscription ID (Abonnement-ID): Azure-Abonnement-ID
* Azure Active Directory Tenant ID (Azure Active Directory-Mandanten-ID): Die ID des Active Directory-Mandanten
* Proxy host (Proxyhost): Hostname des Proxys, wenn SAP LaMa einen Proxy für die Internetverbindung benötigt
* Proxy port (Proxyport): TCP-Port des Proxys
* Speichertyp ändern, um Kosten zu sparen: Aktivieren Sie diese Einstellung, wenn der Azure-Adapter den Speichertyp der verwalteten Datenträger ändern soll, um Kosten zu sparen, wenn die Datenträger nicht verwendet werden. Für Datenträger, auf die in einer SAP-Instanzkonfiguration verwiesen wird, ändert der Adapter den Datenträgertyp bei einer Aufhebung der Vorbereitung der Instanz in Storage Standard und bei der Vorbereitung der Instanz in den ursprünglichen Speichertyp. Wenn Sie einen virtuellen Computer in SAP LaMa beenden, ändert der Adapter den Speichertyp aller angefügten Datenträger, einschließlich des Betriebssystemdatenträgers, in Storage Standard. Wenn Sie einen virtuellen Computer in SAP LaMa starten, ändert der Adapter den Speichertyp wieder in den ursprünglichen Speichertyp.

Klicken Sie auf „Test Configuration“ (Konfiguration testen), um Ihre Eingabe zu überprüfen. Folgendes sollte angezeigt werden:

Verbindung erfolgreich: Verbindung mit Microsoft-Cloud war erfolgreich. 7 Ressourcengruppen gefunden (nur 10 Gruppen angefordert)

(Unten auf der Website)

## <a name="provision-a-new-adaptive-sap-system"></a>Bereitstellen eines neuen adaptiven SAP-Systems

Sie können eine neue VM manuell bereitstellen oder eine Azure-Vorlage im [Schnellstartrepository](https://github.com/Azure/azure-quickstart-templates) verwenden. Das Repository enthält Vorlagen für [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver-Anwendungsserver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps) und die [Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Sie können diese Vorlagen auch verwenden, um neue Hosts beispielsweise als Teil einer Systemkopie/eines Systemklons bereitzustellen.

Es wird empfohlen, für alle VMs, die Sie mit SAP LaMa verwalten möchten, ein eigenes Subnetz und keine dynamischen IP-Adressen zu verwenden, um das „Stehlen“ von IP-Adressen zu verhindern, wenn neue VMs bereitgestellt werden, und SAP-Instanzen nicht vorbereitet sind.

> [!NOTE]
> Wenn möglich, entfernen Sie alle Erweiterungen der VM, da sie lange Laufzeiten beim Trennen von Datenträgern von einer VM verursachen können.

Vergewissern Sie sich, dass die Benutzer „\<hanasid>adm“ und „\<sapsid>adm“ sowie die Gruppe „sapsys“ mit der gleichen ID und GID auf dem Zielcomputer vorhanden sind, oder verwenden Sie LDAP. Aktivieren und starten Sie den NFS-Server auf der VM, auf der SAP NetWeaver (A)SCS ausgeführt werden sollen.

### <a name="manual-deployment"></a>Manuelle Bereitstellung

SAP LaMa kommuniziert mit dem SAP-Host-Agent mit dem virtuellen Computer. Wenn Sie die VMs manuell oder nicht mit Azure Resource Manager-Vorlagen aus dem Schnellstartrepository bereitstellen, überprüfen Sie, ob der aktuelle SAP-Host-Agent und die SAP Adaptive Extensions installiert sind. Weitere Informationen zu den erforderlichen Patchebenen für Azure finden Sie im SAP-Hinweis [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Manuelles Bereitstellen einer Linux-VM

Erstellen Sie eine neue VM mit einem unterstützten Betriebssystem (siehe SAP-Hinweis [2343511]). Fügen Sie den SAP-Instanzen zusätzliche IP-Konfigurationen hinzu. Jede Instanz benötigt mindestens eine IP-Adresse und muss mit einem virtuellen Hostnamen installiert werden.

Die SAP NetWeaver ASCS-Instanz benötigt Datenträger für „/sapmnt/\<SAPSID>“, „/usr/sap/\<SAPSID>“, „/usr/sap/trans“ und „/usr/sap/\<sapsid>adm“. Für die SAP NetWeaver-Anwendungsserver sind keine zusätzlichen Datenträger erforderlich. Alle Elemente mit Bezug auf die SAP-Instanz müssen auf der ASCS-Instanz gespeichert und über NFS exportiert werden. Ansonsten ist es derzeit nicht möglich, weitere Anwendungsserver über SAP LaMa hinzuzufügen.

![SAP NetWeaver ASCS unter Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Manuelles Bereitstellen für SAP HANA

Erstellen Sie eine neue VM mit einem Betriebssystem, das SAP HANA unterstützt (siehe SAP-Hinweis [2343511]). Fügen Sie eine zusätzliche IP-Konfiguration für SAP HANA und eine pro HANA-Mandant hinzu.

SAP HANA benötigt Datenträger für /hana/shared, /hana/backup, /hana/data und /hana/log.

![SAP HANA unter Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuelles Bereitstellen für Oracle Database unter Linux

Erstellen Sie eine neue VM mit einem Betriebssystem, das Oracle-Datenbanken unterstützt (siehe SAP-Hinweis [2343511]). Fügen Sie eine zusätzliche IP-Konfiguration für die Oracle-Datenbank hinzu.

Die Oracle-Datenbank benötigt Datenträger für/oracle, /home/oraod1 und /home/oracle.

![Diagramm: Oracle-Datenbank unter Linux und die erforderlichen Datenträger](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Manuelles Bereitstellen für Microsoft SQL Server

Erstellen Sie eine neue VM mit einem Betriebssystem, das Microsoft SQL Server unterstützt (siehe SAP-Hinweis [2343511]). Fügen Sie eine zusätzliche IP-Konfiguration für die SQL Server-Instanz hinzu.

Der SQL Server-Datenbankserver benötigt Datenträger für Datenbankdaten und Protokolldateien sowie Datenträger für c:\usr\sap.

![Oracle-Datenbank unter Linux](media/lama/sap-lama-db-sql.png)

Stellen Sie sicher, dass Sie einen unterstützten Microsoft ODBC-Treiber für SQL Server auf einer zu verwendenden VM installieren, um einen SAP NetWeaver-Anwendungsserver auf ein oder als Systemkopieziel/Systemklonziel zu verschieben.

SAP LaMa kann SQL Server nicht verschieben. Das bedeutet, auf einer VM, die Sie verwenden möchten, muss SQL Server vorinstalliert sein, um eine Datenbankinstanz auf ein oder als Systemkopieziel/Systemklonziel zu verschieben.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Bereitstellen einer VM mit einer Azure-Vorlage

Laden Sie die folgenden neuesten Archive aus dem [SAP Software Marketplace](https://support.sap.com/swdc) für das VM-Betriebssystem herunter:

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

Laden Sie außerdem die folgenden Komponenten aus dem [Microsoft Download Center](https://www.microsoft.com/download) herunter:

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (nur Windows)
1. Microsoft ODBC Driver for SQL Server (nur SQL Server)

Sie benötigen diese Komponenten, um die Vorlage bereitzustellen. So stellen Sie Komponenten am einfachsten in der Vorlage bereit: Laden Sie sie in einem Azure-Speicherkonto hoch, und erstellen Sie eine SAS (Shared Access Signatur).

Die Vorlagen haben die folgenden Parameter:

* sapSystemId: Die SAP-System-ID. Hiermit wird das Datenträgerlayout erstellt (z. B. „/usr/sap/\<sapsid>“).

* computerName: Der Computername der neuen VM. Dieser Parameter wird auch von SAP LaMa verwendet. Wenn Sie diese Vorlage verwenden, um eine neue VM als Teil einer Systemkopie bereitzustellen, wartet SAP LaMa, bis der Host mit diesem Computernamen erreicht werden kann.

* osType: Der Typ des Betriebssystems, das bereitgestellt werden soll.

* dbtype: Der Typ der Datenbank. Mit diesem Parameter wird festgelegt, wie viele zusätzliche IP-Konfigurationen hinzugefügt werden müssen, und wie das Datenträgerlayout aussehen soll.

* sapSystemSize: Die Größe des SAP-Systems, das bereitgestellt werden soll. Damit werden der Instanztyp und die Größe der VM festgelegt.

* adminUsername: Der Benutzername für die VM.

* adminPassword: Das Kennwort für die VM. Sie können auch einen öffentlichen Schlüssel für SSH bereitstellen.

* sshKeyData: Der öffentliche SSH-Schlüssel für die VMs. Dieser Parameter wird nur unter Linux unterstützt.

* subnetId: Die ID des gewünschten Subnetzes.

* deployEmptyTarget: Sie können ein leeres Ziel bereitstellen, wenn Sie die VM als Ziel für eine Instanzverschiebung o.Ä. verwenden möchten. In diesem Fall werden keine zusätzlichen Datenträger oder IP-Konfigurationen angefügt.

* sapcarLocation: Der Speicherort der sapcar-Anwendung, die dem bereitgestellten Betriebssystem entspricht. Mit „sapcar“ werden Archive extrahiert, die Sie in anderen Parametern angeben.

* sapHostAgentArchiveLocation: Der Speicherort des SAP Host Agent-Archivs. Der SAP-Host-Agent wird als Teil dieser Vorlagenbereitstellung bereitgestellt.

* sapacExtLocation: Der Speicherort der SAP Adaptive Extensions. Der SAP-Hinweis [2343511] führt die mindestens erforderliche Patchebene für Azure auf.

* vcRedistLocation: Der Speicherort der VC-Runtime, die zum Installieren der SAP Adaptive Extensions erforderlich ist. Dieser Parameter ist nur für Windows erforderlich.

* odbcDriverLocation: Der Speicherort des ODBC-Treibers, der installiert werden soll. Es wird nur Microsoft ODBC Driver for SQL Server unterstützt.

* sapadmPassword: Das Kennwort für den Benutzer „sapadm“.

* sapadmId: Die Linux-Benutzer-ID des Benutzers „sapadm“. Dieser Parameter ist für Windows nicht erforderlich.

* sapsysGid: Die Linux-Gruppen-ID der Gruppe „sapsys“. Dieser Parameter ist für Windows nicht erforderlich.

* _artifactsLocation: Der Basis-URI, an dem sich die Artefakte befinden, die für diese Vorlage erforderlich sind. Wenn die Vorlage mit den zugehörigen Skripten bereitgestellt wird, wird ein privater Speicherort im Abonnement verwendet und dieser Wert automatisch generiert. Dieser Parameter ist nur erforderlich, wenn Sie nicht die Vorlage aus GitHub bereitstellen.

* _artifactsLocationSasToken: Das SAS-Token, das für den Zugriff auf _artifactsLocation erforderlich ist. Wenn die Vorlage mit den zugehörigen Skripten bereitgestellt wird, wird ein sasToken automatisch generiert. Dieser Parameter ist nur erforderlich, wenn Sie nicht die Vorlage aus GitHub bereitstellen.

### <a name="sap-hana"></a>SAP HANA

In den folgenden Beispielen wird davon ausgegangen, dass Sie SAP HANA mit der System-ID HN1 und das SAP NetWeaver-System mit der System-ID AH1 installieren. Die virtuellen Hostnamen sind „hn1-db“ für die HANA-Instanz, „ah1-db“ für den vom SAP NetWeaver-System verwendeten HANA-Mandanten, „ah1-ascs“ für SAP NetWeaver ASCS und „ah1-di-0“ für den ersten SAP NetWeaver-Anwendungsserver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Installieren von SAP NetWeaver ASCS für SAP HANA mithilfe von Azure Managed Disks

Bevor Sie SAP Software Provisioning Manager (SWPM) starten, müssen Sie die IP-Adresse des virtuellen Hostnamens von ASCS bereitstellen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

![Linux-Logo][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows-Logo][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Führen Sie SWPM aus, und verwenden Sie *ah1-ascs* als *ASCS-Instanzhostnamen*.

![Linux-Logo][Logo_Linux] Linux  
Fügen Sie den folgenden Profilparameter dem SAP-Host-Agent-Profil hinzu, der sich unter /usr/sap/hostctrl/exe/host_profile befindet. Weitere Informationen finden Sie in SAP-Hinweis [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Installieren von SAP NetWeaver ASCS für SAP HANA auf Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Diese Funktionalität ist noch nicht allgemein verfügbar. Weitere Informationen finden Sie im SAP-Hinweis [2815988] (nur für Kunden der Vorschauversion sichtbar).
Öffnen Sie einen SAP-Incident in der Komponente BC-VCM-LVM-HYPERV, und fordern Sie das Einbinden des LaMa-Speicheradapters für die Azure NetApp Files-Vorschauversion an.

ANF stellt NFS für Azure bereit. Im Zusammenhang mit SAP LaMa vereinfacht dies die Erstellung der ASCS-Instanzen (ABAP Central Services) und der nachfolgenden Installation von Anwendungsservern. Zuvor musste die ASCS-Instanz auch als NFS-Server fungieren, und der Parameter „acosprep/nfs_paths“ musste dem „host_profile“ des SAP-HostAgents hinzugefügt werden.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF ist zurzeit in den folgenden Regionen verfügbar:

Australien, Osten; USA, Mitte; USA, Osten; USA, Osten 2, Europa, Norden; USA, Süden-Mitte, Europa, Westen und USA, Westen 2.

#### <a name="network-requirements"></a>Netzwerkanforderungen

ANF erfordert ein delegiertes Subnetz, das Teil desselben VNET wie bei den SAP-Servern sein muss. Hier ist ein Beispiel für solch eine Konfiguration.
Dieser Bildschirm zeigt die Erstellung des VNET und des ersten Subnetzes:

![SAP LaMa – Erstellen eines virtuellen Netzwerks für Azure ANF ](media/lama/sap-lama-createvn-50.png)

Im nächsten Schritt wird das delegierte Subnetz für „Microsoft.NetApp/volumes“ erstellt.

![SAP LaMa – Hinzufügen eines delegierten Subnetzes ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa – Liste der Subnetze ](media/lama/sap-lama-subnets.png)

Jetzt muss ein NetApp-Konto im Azure-Portal erstellt werden:

![SAP LaMa – Erstellen eines NetApp-Kontos ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa – NetApp-Konto wurde erstellt ](media/lama/sap-lama-netappaccount.png)

Innerhalb des NetApp-Kontos gibt der Kapazitätspool die Größe und den Typ der Datenträger für die einzelnen Pools an:

![SAP LaMa – Erstellen eines NetApp-Kapazitätspools ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa – NetApp-Kapazitätspool wurde erstellt ](media/lama/sap-lama-capacitypool-list.png)

Jetzt können die NFS-Volumes definiert werden. Da es Volumes für mehrere Systeme in einem einzigen Pool geben wird, sollte ein selbsterklärendes Benennungsschema ausgewählt werden. Durch das Hinzufügen der Sicherheits-ID (SID) können verwandte Volumes gruppiert werden. Für ASCS und die AS-Instanz sind die folgenden Einbindungen erforderlich: „ */sapmnt/\<SID\>* “, „ */usr/sap/\<SID\>* “ und „ */home/\<sid\>adm*“. Optional wird */usr/sap/trans* für das zentrale Übertragungsverzeichnis benötigt, das mindestens von allen Systemen einer einzigen Landschaft verwendet wird.

> [!NOTE]
> Während der BETA-Phase muss der Name der Volumes im Abonnement eindeutig sein.

![SAP LaMa – Erstellen eines Volumes 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa – Erstellen eines Volumes 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa – Erstellen eines Volumes 3 ](media/lama/sap-lama-createvolume3-80.png)

Diese Schritte müssen auch bei den anderen Volumes wiederholt werden.

![SAP LaMa – Liste der erstellten Volumes ](media/lama/sap-lama-volumes.png)

Jetzt müssen diese Volumes in den Systemen bereitgestellt werden, in denen die Erstinstallation mit SAP SWPM ausgeführt wird.

Zuerst müssen die Bereitstellungspunkte erstellt werden. In diesem Fall ist die SID „AN1“. Deshalb müssen die folgenden Befehle ausgeführt werden:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Im nächsten Schritt werden die ANF-Volumes mit den folgenden Befehlen bereitgestellt:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Die Bereitstellungsbefehle können auch aus dem Portal abgeleitet werden. Die lokalen Bereitstellungspunkte müssen angepasst werden.

Verwenden Sie zum Überprüfen den Befehl „df-h“.

![SAP LaMa – BS-Ebene für Bereitstellungspunkte ](media/lama/sap-lama-mounts.png)

Jetzt muss die Installation mit SWPM durchgeführt werden.

Dieselben Schritte müssen für mindestens eine AS-Instanz ausgeführt werden.

Nach der erfolgreichen Installation muss das System innerhalb von SAP LaMa erkannt werden.

Die Einstellungspunkte sollten für ASCS und die AS-Instanz ähnlich wie hier aussehen:

![SAP LaMa-Bereitstellungspunkte in LaMa ](media/lama/sap-lama-ascs.png) (Dies ist ein Beispiel. Die IP-Adressen und der Exportpfad unterscheiden sich von den zuvor verwendeten.)


#### <a name="install-sap-hana"></a>Installieren von SAP HANA

Wenn Sie SAP HANA mithilfe dem Befehlszeilentool „hdblcm“ installieren, verwenden Sie Parameter „--hostname“, um einen virtuellen Hostnamen bereitzustellen. Sie müssen die IP-Adresse des virtuellen Hostnamens der Datenbank einer Netzwerkschnittstelle hinzufügen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

Fügen Sie einen weiteren virtuellen Hostnamen und eine IP-Adresse für den Namen hinzu, der von den Anwendungsservern für die Verbindung zum HANA-Mandanten verwendet wird.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Führen Sie die Installation der Datenbankinstanz von SWPM auf der VM des Anwendungsservers aus– nicht auf der HANA-VM. Verwenden Sie *ah1-db* für den *Datenbankhost* im Dialogfeld *Datenbank für SAP-System*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installieren eines SAP NetWeaver-Anwendungsservers für SAP HANA

Bevor Sie SAP Software Provisioning Manager (SWPM) starten, müssen Sie die IP-Adresse des virtuellen Hostnamens des Anwendungsservers bereitstellen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

![Linux-Logo][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows-Logo][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Es wird empfohlen, den SAP NetWeaver-Profilparameter „dbs/hdb/hdb_use_ident“ zu verwenden, um die Identität festzulegen, mit der der Schlüssel im HDB-Benutzerstore gefunden wird. Sie können diesen Parameter nach der Installation der Datenbankinstanz mit SWPM manuell hinzufügen, oder SWPM folgendermaßen ausführen:

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Wenn Sie den Parameter manuell festlegen, müssen Sie auch neue HDB-Benutzerstoreeinträge erstellen.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Verwenden Sie *ah1-di-0* als *PAS-Instanzhostnamen* im Dialogfeld *Primäre Anwendungsserverinstanz*.

#### <a name="post-installation-steps-for-sap-hana"></a>Schritte nach der Installation für SAP HANA

Sichern Sie SYSTEMDB und alle Mandantendatenbanken, bevor Sie einen Mandanten kopieren bzw. verschieben oder eine Systemreplikation erstellen.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

In den folgenden Beispielen wird davon ausgegangen, dass Sie das SAP NetWeaver-System mit der System-ID AS1 installieren. Die virtuellen Hostnamen sind „as1-db“ für die vom SAP NetWeaver-System verwendete SQL Server-Instanz, „as1-ascs“ für SAP NetWeaver ASCS und „as1-di-0“ für den ersten SAP NetWeaver-Anwendungsserver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installieren von SAP NetWeaver ASCS für SQL Server

Bevor Sie SAP Software Provisioning Manager (SWPM) starten, müssen Sie die IP-Adresse des virtuellen Hostnamens von ASCS bereitstellen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Führen Sie SWPM aus, und verwenden Sie *as1-ascs* als *ASCS-Instanzhostnamen*.

#### <a name="install-sql-server"></a>Installieren von SQL Server

Sie müssen die IP-Adresse des virtuellen Hostnamens der Datenbank einer Netzwerkschnittstelle hinzufügen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Führen Sie die Installation der SWPM-Datenbankinstanz auf der SQL Server-VM aus. Verwenden Sie „SAPINST_USE_HOSTNAME=*as1-db*“, um den für die Verbindung zu SQL Server verwendeten Hostnamen zu überschreiben. Wenn Sie die VM mit der Azure Resource Manager-Vorlage bereitgestellt haben, muss das Verzeichnis für die Datenbankdatendateien *C:\sql\data* und das für die Datenbankprotokolldatei *C:\sql\log* lauten.

Stellen Sie sicher, dass der Benutzer *NT AUTHORITY\SYSTEM* auf SQL Server zugreifen kann und die Serverrolle *sysadmin* hat. Weitere Informationen finden Sie in den SAP-Hinweisen [1877727] und [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installieren des SAP NetWeaver-Anwendungsservers

Bevor Sie SAP Software Provisioning Manager (SWPM) starten, müssen Sie die IP-Adresse des virtuellen Hostnamens des Anwendungsservers bereitstellen. Es wird empfohlen, „sapacext“ zu verwenden. Wenn Sie die IP-Adresse mit „sapacext“ bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse nach einem Neustart erneut bereitstellen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Verwenden Sie *as1-di-0* als *PAS-Instanzhostnamen* im Dialogfeld *Primäre Anwendungsserverinstanz*.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="errors-and-warnings-during-discover"></a>Fehler und Warnungen beim Erkunden

* Verweigern der SELECT-Berechtigung
  * [Microsoft][ODBC SQL Server Driver][SQL Server]Die SELECT-Berechtigung wurde für das Objekt „log_shipping_primary_databases“, Datenbank „msdb“, Schema „dbo“ verweigert. [SOAPFaultException]  
  Die SELECT-Berechtigung wurde für das Objekt „log_shipping_primary_databases“, Datenbank „msdb“, Schema „dbo“ verweigert.
  * Lösung  
    Stellen Sie sicher, dass *NT AUTHORITY\SYSTEM* auf SQL Server zugreifen kann. Weitere Informationen finden Sie in SAP-Hinweis [2562184].


### <a name="errors-and-warnings-for-instance-validation"></a>Fehler und Warnungen bei der Instanzprüfung

* Auslösen einer Ausnahme beim Prüfen des HDB-Benutzerstores  
  * Protokollanzeige:  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Ausnahme im Validierungssteuerelement mit der ID „RuntimeHDBConnectionValidator“ (Überprüfung: VALIDATION_HDB_USERSTORE): hdbuserstore konnte nicht abgerufen werden.  
    Der HANA-Benutzerstore ist nicht am richtigen Speicherort.
  * Lösung  
    Überprüfen Sie, ob „/usr/sap/AH1/hdbclient/install/installation.ini“ richtig ist.

### <a name="errors-and-warnings-during-a-system-copy"></a>Fehler und Warnungen beim Kopieren eines Systems

* Fehler beim Überprüfen des Systembereitstellungsschritts
  * Ursache: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException ruft „/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r“ auf | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Lösung  
    Erstellen Sie eine Sicherung aller Datenbanken im HANA-Quellsystem.

* Schritt *Starten* beim Erstellen einer Systemkopie der Datenbankinstanz
  * Der Host-Agent-Vorgang „000D3A282BC91EE8A1D76CF1F92E2944“ ist fehlgeschlagen (OperationException. FaultCode: 127, Nachricht: „Fehler bei der Ausführung des Befehls. : [Microsoft][ODBC SQL Server Driver][SQL Server]Der Benutzer hat keine Berechtigung zum Ändern der Datenbank „AS2“, die Datenbank existiert nicht, oder die Datenbank hat keinen Status, der Zugriffsprüfungen erlaubt.“)
  * Lösung  
    Stellen Sie sicher, dass *NT AUTHORITY\SYSTEM* auf SQL Server zugreifen kann. Weitere Informationen finden Sie in SAP-Hinweis [2562184].

### <a name="errors-and-warnings-during-a-system-clone"></a>Fehler und Warnungen beim Klonen eines Systems

* Fehler beim Registrieren des Instanz-Agenten in Schritt *Registrierung erzwingen und Instanz-Agenten starten* des Anwendungsservers oder von ASCS
  * Der Fehler ist beim Registrierversuch für den Instanz-Agenten aufgetreten. (RemoteException: Fehler beim Laden der Instanzdaten aus dem Profil ‚\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0‘:  Zugriff auf Profil ‚\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0‘ nicht möglich: Datei oder Verzeichnis nicht vorhanden.“)
  * Lösung  
   Stellen Sie sicher, dass die „sapmnt“-Freigabe auf ASCS/SCS Vollzugriff auf SAP_AS1_GlobalAdmin hat.

* Fehler bei Schritt *Startschutz für Klon aktivieren*
  * Datei „\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0“ konnte nicht geöffnet werden. Grund: Datei oder Verzeichnis nicht vorhanden
  * Lösung  
    Das Computerkonto des Anwendungsservers benötigt Schreibzugriff auf das Profil.

### <a name="errors-and-warnings-during-create-system-replication"></a>Fehler und Warnungen beim Erstellen einer Systemreplikation

* Ausnahme beim Klicken zum Erstellen einer Systemreplikation
  * Ursache: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException ruft „/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r“ auf | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Lösung  
    Testen Sie, ob „sapacext“ als „`<hanasid`>adm“ ausgeführt werden kann.

* Fehler bei deaktivierter vollständiger Kopie im Speicherschritt
  * Beim Melden einer Kontextattributnachricht für Pfad „IStorageCopyData.storageVolumeCopyList:1“ und Feld „targetStorageSystemId“ ist ein Fehler aufgetreten.
  * Lösung  
    Ignorieren Sie die Warnungen in diesem Schritt, und versuchen Sie es noch mal. Dieses Problem wird in einem neuen Unterstützungspaket/Patch von SAP LaMa behoben.

### <a name="errors-and-warnings-during-relocate"></a>Fehler und Warnungen beim Verschieben

* Ungültiger Pfad „/usr/sap/AH1“ für NFS-Reexporte
  * Weitere Informationen finden Sie in SAP-Hinweis [2628497].
  * Lösung  
    Fügen Sie dem ASCS-Profil HostAgent ASCS-Exporte hinzu. Weitere Informationen finden Sie im SAP-Hinweis [2628497].

* Funktion nicht implementiert beim Verschieben von ASCS
  * Befehlsausgabe: exportfs: host: /usr/sap/AX1: Funktion nicht implementiert
  * Lösung  
    Überprüfen Sie, ob der NFS-Serverdienst auf der Ziel-VM, auf die verschoben werden soll, aktiviert ist.

### <a name="errors-and-warnings-during-application-server-installation"></a>Fehler und Warnungen beim Installieren des Anwendungsservers

* Fehler beim Ausführen des „SAPinst“-Schritts: getProfileDir
  * FEHLER: (Letzter vom Schritt gemeldeter Fehler: Abgefangene ESAPinstException in Modulaufruf: Validierungssteuerelement für Schritt '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' meldete einen Fehler: Knoten ‚\\\as1-ascs\sapmnt\AS1\SYS\profile‘ ist nicht vorhanden. Starten Sie „SAPinst“ im interaktiven Modus, um dieses Problem zu beheben.)
  * Lösung  
    Stellen Sie sicher, dass SWPM mit einem Benutzer ausgeführt wird, der Zugriff auf das Profil hat. Dieser Benutzer kann im Installations-Assistenten des Anwendungsservers konfiguriert werden.

* Fehler beim Ausführen des „SAPinst“-Schritts: askUnicode
  * FEHLER: (Letzter vom Schritt gemeldeter Fehler: Abgefangene ESAPinstException in Modulaufruf: Validierungssteuerelement für Schritt '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' meldete einen Fehler: Starten Sie „SAPinst“ im interaktiven Modus, um dieses Problem zu beheben.)
  * Lösung  
    Wenn Sie einen aktuellen SAP-Kernel verwenden, kann SWPM nicht mehr feststellen, ob das System ein Unicodesystem ist, indem es den ASCS-Nachrichtenserver verwendet. Weitere Informationen finden Sie im SAP-Hinweis [2445033].  
    Dieses Problem wird in einem neuen Unterstützungspaket/Patch von SAP LaMa behoben.  
    Legen Sie den Profilparameter „OS_UNICODE=uc“ im Standardprofil des SAP-Systems fest, um dieses Problem zu umgehen.

* Fehler beim Ausführen des „SAPinst“-Schritts: dCheckGivenServer
  * Fehler beim Ausführen des „SAPinst“-Schritts: dCheckGivenServer", version="1.0", FEHLER: (Letzter vom Schritt gemeldeter Fehler: \<p> Die Installation wurde vom Benutzer abgebrochen. \</p>
  * Lösung  
    Stellen Sie sicher, dass SWPM mit einem Benutzer ausgeführt wird, der Zugriff auf das Profil hat. Dieser Benutzer kann im Installations-Assistenten des Anwendungsservers konfiguriert werden.

* Fehler beim Ausführen des „SAPinst“-Schritts: checkClient
  * Fehler beim Ausführen des „SAPinst“-Schritts: checkClient", version = "1.0", FEHLER: (Letzter vom Schritt gemeldeter Fehler: \<p> Die Installation wurde vom Benutzer abgebrochen. \</p>)
  * Lösung  
    Überprüfen Sie, ob Microsoft ODBC Driver for SQL Server auf der VM installiert ist, auf der Sie den Anwendungsserver installieren möchten.

* Fehler beim Ausführen des „SAPinst“-Schritts: copyScripts
  * Letzter vom Schritt gemeldeter Fehler: Fehler bei Systemaufruf. DETAILS: Fehler 13 (0x0000000d) (Berechtigung verweigert.) beim Ausführen des Systemaufrufs „fopenU“ mit Parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), Zeile (494) in Datei (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), Stapelüberwachung:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Lösung  
    Stellen Sie sicher, dass SWPM mit einem Benutzer ausgeführt wird, der Zugriff auf das Profil hat. Dieser Benutzer kann im Installations-Assistenten des Anwendungsservers konfiguriert werden.

* Fehler beim Ausführen des „SAPinst“-Schritts: askPasswords
  * Letzter vom Schritt gemeldeter Fehler: Fehler bei Systemaufruf. DETAILS: Fehler 5 (0x00000005) (Zugriff verweigert.) beim Ausführen des Systemaufrufs „NetValidatePasswordPolicy“ mit Parameter (...), Zeile (359) in Datei (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), Stapelüberwachung:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Lösung  
    Fügen Sie in Schritt *Isolation* eine Hostregel hinzu, um die Kommunikation von der VM zum Domänencontroller zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte
* [SAP HANA in Azure – Betriebshandbuch][hana-ops-guide]
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
