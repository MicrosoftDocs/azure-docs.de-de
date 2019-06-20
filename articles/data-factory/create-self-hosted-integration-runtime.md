---
title: Erstellen einer selbstgehosteten Integration Runtime in Azure Data Factory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie in Azure Data Factory eine selbstgehostete Integration Runtime erstellen, mit der Data Factorys das Zugreifen auf Datenspeicher in einem privaten Netzwerk ermöglicht wird.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 90e43ab0448646650067dbf151702132f434c01e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967958"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime
Bei der Integration Runtime (IR) handelt es sich um die Computeinfrastruktur, mit der Azure Data Factory Datenintegrationsfunktionen übergreifend für verschiedene Netzwerkumgebungen bereitstellt. Weitere Informationen zur Integration Runtime finden Sie unter [Integrationslaufzeit in Azure Data Factory](concepts-integration-runtime.md).

Mit einer selbstgehosteten Integration Runtime können Kopieraktivitäten zwischen einem Clouddatenspeicher und einem Datenspeicher im privaten Netzwerk ausgeführt und Transformationsaktivitäten für Computeressourcen in einem lokalen Netzwerk oder einem virtuellen Azure-Netzwerk verteilt werden. Für die Installation einer selbstgehosteten Integration Runtime ist ein lokaler Computer oder ein virtueller Computer (VM) in einem privaten Netzwerk erforderlich.  

In diesem Dokument wird beschrieben, wie Sie die selbstgehostete IR erstellen und konfigurieren können.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Allgemeine Schritte zum Installieren der selbstgehosteten IR
1. Erstellen Sie eine selbstgehostete Integration Runtime. Sie können die Azure Data Factory-Benutzeroberfläche für diese Aufgabe verwenden. Hier ist ein PowerShell-Beispiel angegeben:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Laden](https://www.microsoft.com/download/details.aspx?id=39717) Sie die selbstgehostete Integration Runtime herunter, und installieren Sie sie auf dem lokalen Computer.

3. Rufen Sie den Authentifizierungsschlüssel ab, und registrieren Sie die selbstgehostete Integration Runtime mit dem Schlüssel. Hier ist ein PowerShell-Beispiel angegeben:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Einrichten der selbstgehosteten Integration Runtime auf dem virtuellen Azure-Computer mithilfe einer Azure Resource Manager-Vorlage (Automatisierung)
Sie können das Setup der selbstgehosteten IR auf einem virtuellen Azure-Computer mit [dieser Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime) automatisieren. Diese Vorlage bietet eine einfache Möglichkeit, eine voll funktionsfähige, selbstgehostete IR im virtuellen Azure-Netzwerk mit Features für hohe Verfügbarkeit und Skalierbarkeit zu nutzen (solange Sie die Knotenanzahl auf 2 oder höher festlegen).

## <a name="command-flow-and-data-flow"></a>Befehls- und Datenfluss
Wenn Sie Daten zwischen der lokalen Umgebung und der Cloud verschieben, wird für die Aktivität eine selbstgehostete Integration Runtime verwendet, um die Daten aus der lokalen Datenquelle in die Cloud zu übertragen und umgekehrt.

Hier ist ein allgemeiner Datenfluss als Zusammenfassung der Schritte zum Kopieren per selbstgehosteter IR angegeben:

![Allgemeine Übersicht](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Der Datenentwickler erstellt eine selbstgehostete Integration Runtime in einer Azure Data Factory per PowerShell-Cmdlet. Derzeit wird dieses Feature vom Azure-Portal nicht unterstützt.
2. Der Datenentwickler erstellt einen verknüpften Dienst für einen lokalen Datenspeicher, indem er die Instanz der selbstgehosteten Integration Runtime angibt, die zum Verbinden der Datenspeicher verwendet werden soll.
3. Über den Knoten der selbstgehosteten Integration Runtime werden die Anmeldeinformationen per DPAPI (Windows Data Protection Application Programming Interface) verschlüsselt und lokal gespeichert. Falls mehrere Knoten festgelegt sind, um Hochverfügbarkeit zu erzielen, werden die Anmeldeinformationen für andere Knoten weiter synchronisiert. Jeder Knoten verschlüsselt die Anmeldeinformationen mithilfe von DPAPI und speichert sie lokal. Die Synchronisierung der Anmeldeinformationen ist für den Datenentwickler transparent und wird von der selbstgehosteten IR verarbeitet.    
4. Der Data Factory-Dienst kommuniziert für die Planung und Verwaltung von Aufträgen mit der selbstgehosteten Integration Runtime. Hierfür wird ein *Steuerungskanal* genutzt, der ein freigegebenes [Azure Service Bus Relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) verwendet. Wenn ein Aktivitätsauftrag ausgeführt werden muss, reiht Data Factory die Anforderung zusammen mit den Anmeldeinformationen in die Warteschlange ein (falls Anmeldeinformationen nicht bereits unter der selbstgehosteten Integrationslaufzeit gespeichert sind). Die selbstgehostete Integration Runtime startet den Auftrag, nachdem die Warteschlange abgefragt wurde.
5. Die selbstgehostete Integration Runtime kopiert Daten aus einem lokalen Speicher in einen Cloudspeicher oder in umgekehrter Richtung. Dies hängt davon ab, wie die Kopieraktivität in der Datenpipeline konfiguriert ist. Für diesen Schritt kommuniziert die selbstgehostete Integration Runtime über einen sicheren Kanal (HTTPS) direkt mit einem cloudbasierten Speicherdienst, z.B. Azure Blob Storage.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Aspekte der Nutzung einer selbstgehosteten IR

- Eine einzelne selbstgehostete Integrationslaufzeit kann für mehrere lokale Datenquellen verwendet werden. Eine einzelne selbstgehostete Integration Runtime kann gemeinsam mit einer anderen Data Factory innerhalb des gleichen Azure Active Directory-Mandanten genutzt werden. Weitere Informationen finden Sie unter [Freigeben der selbstgehosteten Integration Runtime (IR) für mehrere Data Factorys](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Sie können auf einem Computer nur eine Instanz der selbstgehosteten Integration Runtime installieren. Wenn Sie über zwei Data Factorys verfügen, die auf lokale Datenquellen zugreifen müssen, verwenden Sie entweder die [Funktion für selbstgehostete IR-Freigabe](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories), um die selbstgehostete Integration Runtime freizugeben, oder installieren Sie die selbstgehostete Integration Runtime auf zwei lokalen Computern, eine für jede Data Factory.  
- Die selbstgehostete Integration Runtime muss sich nicht auf demselben Computer wie die Datenquelle befinden. Wenn sich die selbstgehostete Integration Runtime näher an der Datenquelle befindet, dauert es weniger lange, bis die selbstgehostete Integration Runtime eine Verbindung mit der Datenquelle hergestellt hat. Es wird empfohlen, die selbstgehostete Integrationslaufzeit auf einem anderen Computer als dem Computer zu installieren, auf dem die lokale Datenquelle gehostet wird. Wenn sich die selbstgehostete Integration Runtime und die Datenquelle auf unterschiedlichen Computern befinden, steht die selbstgehostete Integration Runtime mit der Datenquelle nicht im Wettbewerb um Ressourcen.
- Sie können über mehrere selbstgehostete Integration Runtimes auf verschiedenen Computern verfügen, die eine Verbindung mit der gleichen lokalen Datenquelle herstellen. Beispielsweise können Sie über zwei selbstgehostete Integration Runtimes verfügen, die zwei Data Factorys mit Daten versorgen, wobei aber dieselbe lokale Datenquelle für beide Data Factorys registriert ist.
- Falls Sie auf Ihrem Computer bereits ein Gateway installiert haben, das für ein Power BI-Szenario verwendet wird, installieren Sie auf einem anderen Computer eine separate selbstgehostete Integration Runtime für die Azure Data Factory.
- Die selbstgehostete Integration Runtime muss verwendet werden, um die Datenintegration im virtuellen Azure-Netzwerk zu unterstützen.
- Behandeln Sie Ihre Datenquelle wie eine lokale Datenquelle (die sich hinter einer Firewall befindet), selbst wenn Sie Azure ExpressRoute verwenden. Verwenden Sie die selbstgehostete Integrationslaufzeit, um die Konnektivität zwischen dem Dienst und der Datenquelle herzustellen.
- Sie müssen die selbstgehostete Integration Runtime auch verwenden, wenn sich der Datenspeicher in der Cloud auf einem virtuellen Azure IaaS-Computer befindet.
- In einer selbstgehosteten Integration Runtime-Instanz, die auf einem Windows Server-Computer mit aktivierter FIPS-konformer Verschlüsselung installiert ist, schlagen Aufgaben u.U. fehl. Deaktivieren Sie die FIPS-konforme Verschlüsselung auf dem Server, um dieses Problem zu umgehen. Zum Deaktivieren der FIPS-konformen Verschlüsselung ändern Sie den folgenden Registrierungswert von 1 (aktiviert) in 0 (deaktiviert): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Voraussetzungen

- Die unterstützten Betriebssystemversionen sind Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 und Windows Server 2016. Die Installation der selbstgehosteten Integration Runtime auf einem Domänencontroller wird nicht unterstützt.
- .NET Framework 4.6.1 oder höher ist erforderlich. Wenn Sie die selbstgehostete Integration Runtime auf einem Windows 7-Computer installieren, sollten Sie .NET Framework 4.6.1 oder höher installieren. Ausführlichere Informationen finden Sie unter [Systemanforderungen für .NET Framework](/dotnet/framework/get-started/system-requirements) .
- Die empfohlene Konfiguration für den Computer mit der selbstgehosteten Integration Runtime lautet wie folgt: mindestens 2GHz, vier Kerne, 8GB RAM und ein Datenträger mit 80GB.
- Wenn sich der Hostcomputer im Ruhezustand befindet, reagiert die selbstgehostete Integrationslaufzeit nicht auf Datenanforderungen. Konfigurieren Sie vor der Installation der selbstgehosteten Integration Runtime einen entsprechenden Energiesparplan auf dem Computer. Wenn für den Computer der Ruhezustand konfiguriert ist, wird bei der Installation der selbstgehosteten Integrationslaufzeit eine Meldung angezeigt.
- Sie müssen der Administrator des Computers sein, um die selbstgehostete Integrationslaufzeit erfolgreich installieren und konfigurieren zu können.
- Ausführungen der Kopieraktivität geschehen in einer bestimmten Häufigkeit. Die Ressourcenverwendung (CPU, Arbeitsspeicher) auf dem Computer folgt dem gleichen Muster mit Spitzen- und Leerlaufzeiten. Die Ressourcenverwendung hängt auch stark von der Datenmenge ab, die verschoben wird. Wenn mehrere Kopieraufträge in Bearbeitung sind, steigt die Ressourcenverwendung zu Spitzenzeiten an.

## <a name="installation-best-practices"></a>Bewährte Methoden für die Installation
Sie können die selbstgehostete Integration Runtime installieren, indem Sie aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) ein MSI-Setuppaket herunterladen. Im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](tutorial-hybrid-copy-powershell.md) finden Sie eine Schritt-für-Schritt-Anleitung.

- Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für die selbstgehostete Integration Runtime, damit der Computer nicht in den Ruhezustand versetzt wird. Wenn der Hostcomputer in den Ruhezustand versetzt wird, wechselt die selbstgehostete Integration Runtime in den Offlinemodus.
- Sichern Sie regelmäßig die Anmeldeinformationen, die der selbstgehosteten Integrationslaufzeit zugeordnet sind.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Installieren und Registrieren der selbstgehosteten IR über das Download Center

1. Navigieren Sie zur [Downloadseite für die Microsoft-Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Wählen Sie **Herunterladen**, wählen Sie die 64-Bit-Version (32-Bit wird nicht unterstützt), und wählen Sie **Weiter**.
3. Führen Sie die MSI-Datei direkt aus, oder speichern Sie sie zur späteren Ausführung auf Ihrer Festplatte.
4. Wählen Sie auf der **Willkommensseite** eine Sprache und dann **Weiter** aus.
5. Akzeptieren Sie die Microsoft-Software-Lizenzbedingungen, und wählen Sie **Weiter** aus.
6. Wählen Sie den **Ordner** für die Installation der selbstgehosteten Integration Runtime und dann **Weiter** aus.
7. Wählen Sie auf der Seite **Bereit zur Installation** die Option **Installieren** aus.
8. Klicken Sie auf **Fertig stellen** , um die Installation abzuschließen.
9. Rufen Sie den Authentifizierungsschlüssel über Azure PowerShell ab. Hier ist ein PowerShell-Beispiel zum Abrufen des Authentifizierungsschlüssels:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Führen Sie im Konfigurations-Manager für Microsoft Integration Runtime, der auf Ihrem Computer ausgeführt wird, auf der Seite **Integrationslaufzeit (selbstgehostet) registrieren** die folgenden Schritte aus:

    a. Fügen Sie im Textbereich den Authentifizierungsschlüssel ein.

    b. Optional: Wählen Sie **Authentifizierungsschlüssel anzeigen**, um den Schlüsseltext anzuzeigen.

    c. Wählen Sie **Registrieren**.


## <a name="high-availability-and-scalability"></a>Hochverfügbarkeit und Skalierbarkeit
Eine selbstgehostete Integration Runtime kann mehreren lokalen Computern oder Virtual Machines in Azure zugeordnet werden. Diese Computer werden als Knoten bezeichnet. Einer selbstgehosteten Integration Runtime können bis zu vier Knoten zugeordnet sein. Die Vorteile der Nutzung mehrerer Knoten (lokale Computer mit installiertem Gateway) für ein logisches Gateway sind:
* Höhere Verfügbarkeit der selbstgehosteten Integration Runtime, damit es sich nicht mehr um die einzige Fehlerquelle (Single Point of Failure) in Ihrer Big Data-Lösung oder Clouddatenintegration mit Azure Data Factory handelt. Auf diese Weise wird die Kontinuität mit bis zu vier Knoten sichergestellt.
* Verbesserung in Bezug auf die Leistung und den Durchsatz während der Datenverschiebung zwischen lokalen und Clouddatenspeichern. Informieren Sie sich über [Leistungsvergleiche](copy-activity-performance.md).

Sie können durch Installation der Software für die selbstgehostete Integration Runtime aus dem [Downloadcenter](https://www.microsoft.com/download/details.aspx?id=39717) mehrere Knoten zuordnen. Registrieren Sie sie dann mit einem der vom **New-AzDataFactoryV2IntegrationRuntimeKey**-Cmdlet abgerufenen Authentifizierungsschlüssel, wie im [Tutorial](tutorial-hybrid-copy-powershell.md) beschrieben.

> [!NOTE]
> Für die Zuordnung zu den einzelnen Knoten müssen Sie nicht jeweils eine neue selbstgehostete Integration Runtime erstellen. Sie können die selbstgehostete Integration Runtime auf einem anderen Computer installieren und mit dem gleichen Authentifizierungsschlüssel registrieren. 

> [!NOTE]
> Vergewissern Sie sich vor dem Hinzufügen eines weiteren Knotens für Hochverfügbarkeit und Skalierbarkeit, dass die Option **Remotezugriff auf das Intranet** für den ersten Knoten aktiviert ist (**Konfigurations-Manager für Microsoft Integration Runtime** > **Einstellungen** > **Remotezugriff auf das Intranet**). 

### <a name="scale-considerations"></a>Aspekte der Skalierung

#### <a name="scale-out"></a>Horizontales Skalieren

Wenn für die selbstgehostete IR nur wenig Arbeitsspeicher verfügbar und die CPU-Auslastung hoch ist, ist das Hinzufügen eines neuen Knotens hilfreich, um die Last durch horizontales Hochskalieren auf die Computer zu verteilen. Falls für Aktivitäten Fehler auftreten, weil es zu Zeitüberschreitungen kommt oder der selbstgehostete IR-Knoten offline ist, ist es ratsam, dem Gateway einen Knoten hinzuzufügen.

#### <a name="scale-up"></a>Zentrales Hochskalieren

Wenn der verfügbare Speicher und die CPU nicht gut ausgelastet sind, aber die gleichzeitige Ausführung von Aufträgen den Grenzwert erreicht, sollten Sie die Anzahl der gleichzeitigen Aufträge erhöhen, die auf einem Knoten ausgeführt werden können. Es kann auch hilfreich sein, das zentrale Hochskalieren durchzuführen, wenn für Aktivitäten eine Zeitüberschreitung auftritt, weil die selbstgehostete IR überlastet ist. Wie in der folgenden Abbildung gezeigt, können Sie die maximale Kapazität für einen Knoten erhöhen:  

![Heraufsetzen der Anzahl gleichzeitiger Aufträge, die auf einem Knoten ausgeführt werden können](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-Zertifikatanforderungen

Hier sind die Anforderungen für das TLS/SSL-Zertifikat angegeben, das zum Schützen der Kommunikation zwischen Integration Runtime-Knoten verwendet wird:

- Das Zertifikat muss ein öffentlich vertrauenswürdiges Zertifikat vom Typ „X509 v3“ sein. Wir empfehlen Ihnen die Verwendung von Zertifikaten, die von einer öffentlichen Zertifizierungsstelle (Partner) ausgestellt werden.
- Jeder Integration Runtime-Knoten muss diesem Zertifikat vertrauen.
- SAN-Zertifikate (Subject Alternative Name, alternativer Antragstellername) sind nicht empfehlenswert, da nur das letzte SAN-Element verwendet wird und alle anderen aufgrund aktueller Einschränkungen ignoriert werden. Wenn Sie z.B. ein SAN-Zertifikat mit den SANs **node1.domain.contoso.com** und **node2.domain.contoso.com** haben, können Sie dieses Zertifikat nur auf dem Computer mit dem FQDN **node2.domain.contoso.com** verwenden.
- Das Zertifikat unterstützt alle Schlüsselgrößen, die von Windows Server 2012 R2 für SSL-Zertifikate unterstützt werden.
- Zertifikate, die CNG-Schlüssel verwenden, werden nicht unterstützt.  

> [!NOTE]
> Dieses Zertifikat wird zum Verschlüsseln von Ports auf dem selbstgehosteten IR-Knoten verwendet, die für die **Knoten-zu-Knoten-Kommunikation** (für die Statussynchronisierung, die die Synchronisierung der Anmeldeinformationen verknüpfter Dienste über Knoten hinweg umfasst) verwendet werden, und bei der **Verwendung des PowerShell-Cmdlets für die Anmeldeinformationseinstellung des verknüpften Diensts** im lokalen Netzwerk. Es wird empfohlen, dieses Zertifikat zu verwenden, wenn Ihre private Netzwerkumgebung nicht sicher ist oder Sie auch die Kommunikation zwischen Knoten in Ihrem privaten Netzwerk sichern möchten. Das Verschieben von Daten während der Übertragung von der selbstgehosteten IR in andere Datenspeicher erfolgt immer über einen verschlüsselten Kanal, unabhängig von der Verwendung dieses Zertifikats. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Freigeben der selbstgehosteten Integration Runtime für mehrere Data Factorys

Sie können eine vorhandene selbstgehostete Integration Runtime-Infrastruktur wiederverwenden, die Sie bereits in einer Data Factory eingerichtet haben. Dies ermöglicht Ihnen das Erstellen einer *verknüpften selbstgehosteten Integration Runtime* in einer anderen Factory durch Verweisen auf eine vorhandene selbstgehostete IR (freigegeben).

Informationen zum Freigeben einer selbstgehosteten Integration Runtime mit PowerShell finden Sie unter [Erstellen einer freigegebenen selbstgehosteten Integration Runtime in Azure Data Factory mit PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Das folgende Video enthält eine zwölfminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Begriff

- **Freigegebene IR**: Die ursprüngliche selbstgehostete IR, die in einer physischen Infrastruktur ausgeführt wird.  
- **Verknüpfte IR**: Die IR, die auf eine andere freigegebene IR verweist. Dies ist eine logische IR. Sie nutzt die Infrastruktur einer anderen selbstgehosteten IR (freigegeben).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Allgemeine Schritte zum Erstellen einer verknüpften selbstgehosteten IR

1. Erteilen Sie in der selbstgehosteten IR, die freigegeben werden soll, Berechtigungen für die Data Factory, in der Sie die verknüpfte IR erstellen möchten. 

   ![Schaltfläche zum Erteilen von Berechtigungen auf der Registerkarte „Freigabe“](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Auswahl zum Zuweisen von Berechtigungen](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Notieren Sie sich die Ressourcen-ID der selbstgehosteten IR, die freigegeben werden soll.

   ![Speicherort der Ressourcen-ID](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Erstellen Sie in der Data Factory, für die Berechtigungen erteilt wurden, eine neue selbstgehostete IR (verknüpft), und geben Sie die Ressourcen-ID ein.

   ![Schaltfläche zum Erstellen einer verknüpften selbstgehosteten Integration Runtime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Felder für Name und Ressourcen-ID](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Überwachung 

- **Freigegebene IR**

  ![Auswahl für die Suche nach einer freigegebenen Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Registerkarte für Überwachung](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Verknüpfte IR**

  ![Auswahl für die Suche nach einer verknüpften Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Registerkarte für Überwachung](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Bekannte Einschränkungen der Freigabe selbstgehosteter IRs

* Die Data Factory, in der eine verknüpfte IR erstellt werden soll, muss eine [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) haben. Standardmäßig wird für die im Azure-Portal oder mit PowerShell-Cmdlets erstellten Data Factorys implizit eine MSI erstellt. Aber wenn eine Data Factory mit einer Azure Resorce Manager-Vorlage oder dem SDK erstellt wird, muss die Eigenschaft **Identity** explizit festgelegt werden, um sicherzustellen, dass Azure Resource Manager eine Data Factory mit einer MSI erstellt. 

* Version 1.1.0 oder höher des Azure Data Factory .NET SDK unterstützt dieses Feature.

* Zum Gewähren von Berechtigungen muss der Benutzer über die Rolle „Besitzer“ oder die geerbte Rolle „Besitzer“ in der Data Factory verfügen, in der die freigegebene IR vorhanden ist.

* Die Freigabefunktion kann nur für Data Factorys im gleichen Azure Active Directory-Mandanten verwendet werden.

* Für [Gastbenutzer](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) von Active Directory funktioniert die Suchfunktionalität (Auflisten aller Data Factorys mithilfe eines Suchschlüsselworts) in der Benutzeroberfläche [nicht](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Aber so lange der Gastbenutzer der Besitzer der Data Factory ist, kann er die IR ohne Suchfunktion freigeben, indem er die MSI-Datei der Data Factory, mit der die IR freigegeben werden muss, direkt im Textfeld **Berechtigung zuweisen** eingibt und **Hinzufügen** in der Azure Data Factory-Benutzeroberfläche auswählt. 

  > [!NOTE]
  > Dieses Feature steht nur in Azure Data Factory V2 zur Verfügung. 

## <a name="notification-area-icons-and-notifications"></a>Symbole des Benachrichtigungsbereichs und Benachrichtigungen

Wenn Sie Ihren Cursor auf das Symbol bzw. die Nachricht im Benachrichtigungsbereich bewegen, können Sie auf Details zum Status der selbstgehosteten Integration Runtime zugreifen.

![Benachrichtigungen im Benachrichtigungsbereichs](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Ports und Firewall
Zwei Firewalls müssen berücksichtigt werden: Die *Unternehmensfirewall*, die auf dem zentralen Router des Unternehmens ausgeführt wird, und die *Windows-Firewall*, die als Daemon auf dem lokalen Computer mit der selbstgehosteten Integration Runtime konfiguriert ist.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

Auf Ebene der *Unternehmensfirewall* müssen Sie die folgenden Domänen und ausgehenden Ports konfigurieren:

Domänennamen | Ports | BESCHREIBUNG
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Wird für die Kommunikation mit dem Back-End-Datenverschiebungsdienst verwendet
*.core.windows.net | 443 | Wird für das gestaffelte Kopieren über einen Azure-Blobspeicher (sofern konfiguriert) verwendet
*.frontend.clouddatahub.net | 443 | Wird für die Kommunikation mit dem Back-End-Datenverschiebungsdienst verwendet
download.microsoft.com | 443 | Wird zum Herunterladen der Aktualisierungen verwendet

Auf Ebene der *Windows-Firewall* (Computerebene) sind diese ausgehenden Ports normalerweise aktiviert. Falls nicht, können Sie die Domänen und Ports auf dem Computer mit der selbstgehosteten Integration Runtime entsprechend konfigurieren.

> [!NOTE]
> Basierend auf Ihrer Quelle und Ihren Senken müssen Sie unter Umständen der Whitelist Ihrer Unternehmens- oder Windows-Firewall zusätzliche Domänen und ausgehende Ports hinzufügen.
>
> Für einige Clouddatenbanken (z.B. Azure SQL-Datenbank und Azure Data Lake) müssen Sie die IP-Adressen der Computer mit der selbstgehosteten Integration Runtime für die Firewallkonfiguration ggf. auf eine Whitelist setzen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopieren von Daten von einer Quelle in eine Senke
Stellen Sie sicher, dass die Firewallregeln für die Unternehmensfirewall, die Windows-Firewall auf dem Computer mit der selbstgehosteten Integration Runtime und den Datenspeicher selbst richtig aktiviert sind. Bei Aktivierung dieser Regeln kann die selbstgehostete Integrationslaufzeit erfolgreich eine Verbindung mit der Quelle und der Senke herstellen. Aktivieren Sie die Regeln für jeden Datenspeicher, der am Kopiervorgang beteiligt ist.

Führen Sie beispielsweise die folgenden Schritte aus, um Daten aus einem lokalen Datenspeicher in eine Azure SQL-Datenbank-Senke oder eine Azure SQL Data Warehouse-Senke zu kopieren:

1. Lassen Sie ausgehende TCP-Kommunikation an Port 1433 sowohl für die Windows-Firewall als auch die Unternehmensfirewall zu.
2. Konfigurieren Sie die Firewalleinstellungen der Azure SQL Datenbank, um die IP-Adresse des Computers mit der selbstgehosteten Integration Runtime der Liste mit den zulässigen IP-Adressen hinzuzufügen.

> [!NOTE]
> Falls Ihre Firewall den ausgehenden Port 1433 nicht zulässt, kann die selbstgehostete Integration Runtime nicht direkt auf die Azure SQL Datenbank zugreifen. In diesem Fall können Sie ein [gestaffeltes Kopieren](copy-activity-performance.md) zu Azure SQL-Datenbank und Azure SQL Data Warehouse anwenden. Sie benötigen in einem solchen Szenario nur HTTPS (Port 443) für die Datenverschiebung.


## <a name="proxy-server-considerations"></a>Proxyserver-Aspekte
Konfigurieren Sie die selbstgehostete Integration Runtime mit den geeigneten Proxyeinstellungen, wenn die Netzwerkumgebung Ihres Unternehmens einen Proxyserver für den Internetzugriff verwendet. Sie können den Proxy während der anfänglichen Registrierungsphase festlegen.

![Angeben des Proxys](media/create-self-hosted-integration-runtime/specify-proxy.png)

Wenn die selbstgehostete Integration Runtime konfiguriert ist, verwendet sie den Proxyserver zum Herstellen der Verbindung mit dem Clouddienst, Quelle/Ziel (die das HTTP/HTTPS-Protokoll verwenden). Das heißt: Wählen Sie während des Anfangssetups **Verknüpfung ändern** aus. Daraufhin wird das Dialogfeld für Proxyeinstellungen angezeigt.

![Festlegen des Proxys](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Es gibt drei Konfigurationsoptionen:

- **Proxy nicht verwenden**: Für die selbstgehostete IR wird nicht explizit ein Proxy verwendet, um eine Verbindung mit Clouddiensten herzustellen.
- **Systemproxy verwenden**: Die selbstgehostete IR verwendet die in „diahost.exe.config“ und „diawp.exe.config“ konfigurierten Proxyeinstellungen. Wenn in „diahost.exe.config“ und „diawp.exe.config“ kein Proxy konfiguriert ist, stellt die selbstgehostete Integration Runtime die Verbindung mit den Clouddiensten nicht über einen Proxy, sondern direkt her.
- **Benutzerdefinierten Proxy verwenden**: Konfigurieren Sie hier die HTTP-Proxyeinstellungen, die für die selbstgehostete Integration Runtime verwendet werden sollen, anstatt die Konfigurationen in den Dateien „diahost.exe.config“ und „diawp.exe.config“ zu nutzen. **Adresse** und **Port** sind erforderlich. **Benutzername** und **Kennwort** sind je nach den Authentifizierungseinstellungen Ihres Proxys optional. Alle Einstellungen werden für die selbstgehostete Integrationslaufzeit per Windows DPAPI verschlüsselt und lokal auf dem Computer gespeichert.

Der Hostdienst der Integrationslaufzeit wird automatisch neu gestartet, nachdem Sie die aktualisierten Proxyeinstellungen gespeichert haben.

Wenn Sie nach der erfolgreichen Registrierung der selbstgehosteten Integration Runtime die Proxyeinstellungen anzeigen oder aktualisieren möchten, können Sie den Konfigurations-Manager für die Integration Runtime verwenden.

1. Öffnen Sie den **Konfigurations-Manager für Microsoft Integration Runtime**.
2. Wechseln Sie zur Registerkarte **Einstellungen**.
3. Wählen Sie den Link **Ändern** im Abschnitt **HTTP-Proxy** aus, um das Dialogfeld **HTTP-Proxy festlegen** zu öffnen.
4. Klicken Sie auf **Weiter**. Dann wird eine Warnung angezeigt, die Sie zur Bestätigung auffordert, dass die Proxyeinstellung gespeichert und der Hostdienst der Integration Runtime neu gestartet werden soll.

Sie können den HTTP-Proxy im Konfigurations-Manager anzeigen und aktualisieren.

![Anzeigen des Proxys](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Wenn Sie einen Proxyserver mit NTLM-Authentifizierung einrichten, wird der Hostdienst der Integration Runtime unter dem Domänenkonto ausgeführt. Wenn Sie das Kennwort für das Domänenkonto später ändern, denken Sie daran, die Konfigurationseinstellungen für den Dienst entsprechend zu aktualisieren und neu zu starten. Aufgrund dieser Anforderung empfiehlt es sich, ein dediziertes Domänenkonto für den Zugriff auf den Proxyserver zu verwenden, in dem das Kennwort nicht regelmäßig geändert werden muss.

### <a name="configure-proxy-server-settings"></a>Konfigurieren von Proxyservereinstellungen

Wenn Sie die Einstellung **Systemproxy verwenden** für den HTTP-Proxy auswählen, verwendet die selbstgehostete Integration Runtime die Proxyeinstellungen in „diahost.exe.config“ und „diawp.exe.config“. Falls in „diahost.exe.config“ und „diawp.exe.config“ kein Proxy angegeben ist, stellt die selbstgehostete Integration Runtime die Verbindung mit den Clouddiensten nicht über einen Proxy, sondern direkt her. Das folgende Verfahren enthält Anweisungen für die Aktualisierung der Datei „diahost.exe.config“:

1. Erstellen Sie im Datei-Explorer eine sichere Kopie von „C:\Programme\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config“, um die Originaldatei zu sichern.
2. Öffnen Sie „Notepad.exe“ als Administrator, und öffnen Sie die Textdatei „C:\Programme\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config“. Sie finden das Standardtag für „system.net“, wie im folgenden Code gezeigt:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Anschließend können Sie die Informationen zum Proxyserver wie im folgenden Beispiel gezeigt hinzufügen:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Zusätzliche Eigenschaften sind im Proxytag zulässig, um die erforderlichen Einstellungen anzugeben, z. B. `scriptLocation`. Die Syntax finden Sie unter [<proxy>-Element (Netzwerkeinstellungen)](https://msdn.microsoft.com/library/sa91de1e.aspx).

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Speichern Sie die Konfigurationsdatei am ursprünglichen Speicherort. Starten Sie dann den Hostdienst für die selbstgehostete Integration Runtime, der die Änderungen übernimmt. 

   Starten Sie den Dienst mit dem Dienste-Applet in der Systemsteuerung neu. Alternativ wählen Sie im Konfigurations-Manager für Integration Runtime die Schaltfläche **Dienst beenden** und dann **Dienst starten** aus. 
   
   Wenn der Dienst nicht gestartet wird, wurde der bearbeiteten Anwendungskonfigurationsdatei wahrscheinlich eine falsche XML-Tagsyntax hinzugefügt.

> [!IMPORTANT]
> Vergessen Sie nicht, beide Dateien – „diahost.exe.config“ und „diawp.exe.config“ – zu aktualisieren.

Sie müssen auch sicherstellen, dass Microsoft Azure in der Whitelist Ihres Unternehmens aufgeführt ist. Sie können die Liste mit den gültigen Microsoft Azure-IP-Adressen im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen.

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Mögliche Symptome für Probleme im Zusammenhang mit der Firewall und dem Proxyserver
Wenn Sie ähnliche Fehler wie die unten aufgeführten feststellen, liegt dies meist an einer unsachgemäßen Konfiguration der Firewall oder des Proxyservers. Hierdurch wird verhindert, dass die selbstgehostete Integration Runtime eine Verbindung mit der Data Factory herstellt, um sich zu authentifizieren. Um sicherzustellen, dass die Firewall und der Proxyserver richtig konfiguriert sind, überprüfen Sie den vorherigen Abschnitt.

* Wenn Sie versuchen, die selbstgehostete Integration Runtime zu registrieren, erhalten Sie den folgenden Fehler: „Fehler beim Registrieren dieses Knotens der Integrationslaufzeit. Stellen Sie sicher, dass der Authentifizierungsschlüssel gültig ist und der Hostdienst des Integrationsdiensts auf diesem Computer ausgeführt wird.
* Wenn Sie den Konfigurations-Manager für die Integration Runtime öffnen, wird der Status als **Getrennt** oder **Verbindung wird hergestellt** angezeigt. Beim Anzeigen der Windows-Ereignisprotokolle sehen Sie unter **Ereignisanzeige** > **Anwendungs- und Dienstprotokolle** > **Microsoft Integration Runtime** beispielsweise folgende Fehlermeldung:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Aktivieren des Remotezugriffs über das Intranet  
Wenn Sie PowerShell verwenden, um Anmeldeinformationen eines anderen Computers im Netzwerk zu verschlüsseln, auf dem die selbstgehostete Integration Runtime nicht installiert ist, können Sie die Option **Remotezugriff über das Intranet** aktivieren. Wenn Sie PowerShell verwenden, um Anmeldeinformationen auf dem gleichen Computer zu verschlüsseln, auf dem die selbstgehostete Integration Runtime installiert ist, können Sie **Remotezugriff über das Intranet** nicht aktivieren.

Sie sollten den **Remotezugriff über das Intranet** aktivieren, bevor Sie einen weiteren Knoten für Hochverfügbarkeit und Skalierbarkeit hinzufügen.  

Während der Einrichtung der selbstgehosteten Integration Runtime (ab Version 3.3.xxxx.x) wird **Remotezugriff über das Intranet** durch die Installation der selbstgehosteten Integration Runtime auf dem Computer für die selbstgehostete Integration Runtime deaktiviert.

Bei Verwendung einer Drittanbieterfirewall können Sie den Port 8060 (oder den vom Benutzer konfigurierten Port) manuell öffnen. Falls beim Einrichten der selbstgehosteten Integration Runtime Firewallprobleme auftreten, versuchen Sie mit dem folgenden Befehl, die selbstgehostete Integration Runtime zu installieren, ohne die Firewall zu konfigurieren:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
``` 

Falls Sie den Port 8060 auf dem Computer für die selbstgehostete Integration Runtime nicht öffnen, sollten Sie andere Verfahren als die Anwendung „Anmeldeinformationen festlegen“ nutzen, um Anmeldeinformationen für den Datenspeicher zu konfigurieren. Beispielsweise können Sie das **New-AzDataFactoryV2LinkedServiceEncryptCredential**-PowerShell-Cmdlet verwenden.


## <a name="next-steps"></a>Nächste Schritte
Das folgende Tutorial enthält eine Schrittanleitung: [Tutorial: Kopieren von lokalen Daten in die Cloud](tutorial-hybrid-copy-powershell.md).
