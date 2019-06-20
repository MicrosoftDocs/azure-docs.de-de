---
title: Übersicht über die Azure Service Fabric-Sicherheit | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Azure Service Fabric-Sicherheit.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610886"
---
# <a name="azure-service-fabric-security-overview"></a>Übersicht über die Azure Service Fabric-Sicherheit
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht. Service Fabric bietet einfache Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von Cloudanwendungen. Entwickler und Administratoren können komplexe Infrastrukturprobleme vermeiden und sich auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind.

Dieser Artikel enthält eine Übersicht der Sicherheitsüberlegungen für eine Service Fabric-Bereitstellung.

## <a name="secure-your-cluster"></a>Schützen Ihres Clusters
Azure Service Fabric orchestriert Dienste in einem Cluster von Computern. Cluster müssen geschützt werden, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung mit dem Cluster herstellen können, insbesondere dann, wenn im Cluster Produktionsworkloads ausgeführt werden. Obwohl es möglich ist, einen ungeschützten Cluster zu erstellen, kann in diesem Fall jeder anonyme Benutzer eine Verbindung zu dem Cluster herstellen (sofern er Verwaltungsendpunkte im öffentlichen Internet verfügbar macht).

Für Cluster, die entweder eigenständig oder in Azure ausgeführt werden, sind zwei Szenarios denkbar: Knoten-zu-Knoten-Sicherheit und Client-zu-Knoten-Sicherheit. Sie können unterschiedliche Technologien zum Implementieren dieser Szenarios verwenden.

### <a name="node-to-node-security"></a>Knoten-zu-Knoten-Sicherheit
Die Knoten-zu-Knoten-Sicherheit dient zum Schutz der Kommunikation zwischen den virtuellen und physischen Computern in einem Cluster. Mithilfe der Knoten-zu-Knoten-Sicherheit wird gewährleistet, dass nur Computer, die zum Beitreten zum Cluster berechtigt sind, Anwendungen und Dienste im Cluster hosten können.

In Azure ausgeführte Cluster oder eigenständige unter Windows ausgeführte Cluster können für Windows Server-Computer entweder die [Zertifikatsicherheit](https://msdn.microsoft.com/library/ff649801.aspx) oder [Windows-Sicherheit](https://msdn.microsoft.com/library/ff649396.aspx) verwenden.

#### <a name="node-to-node-certificate-security"></a>Zertifikatsicherheit (Knoten zu Knoten)

Service Fabric verwendet X.509-Serverzertifikate, die Sie angeben, wenn Sie einen Cluster erstellen. Ein schneller Überblick darüber, was diese Zertifikate sind und wie Sie sie abrufen oder erstellen können, finden Sie unter [Arbeiten mit Zertifikaten](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Sie konfigurieren die Zertifikatsicherheit beim Erstellen des Clusters über das Azure-Portal, über Azure Resource Manager-Vorlagen oder über eine eigenständige JSON-Vorlage. Sie können ein primäres Zertifikat und ein optionales sekundäres Zertifikat für Zertifikatrollover angeben. Die angegebenen primären und sekundären Zertifikate müssen sich von den Administratorclientzertifikaten und den schreibgeschützten Clientzertifikaten unterscheiden, die Sie für die [Client-zu-Knoten-Sicherheit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) angeben.

### <a name="client-to-node-security"></a>Client-zu-Knoten-Sicherheit
Sie konfigurieren die Client-zu-Knoten-Sicherheit mithilfe von Clientidentitäten. Um die Vertrauensstellung zwischen einem Client und einem Cluster herzustellen, müssen Sie den Cluster so konfigurieren, dass ermittelt werden kann, welche Clientidentitäten vertrauenswürdig sind.

Service Fabric unterstützt zwei unterschiedliche Zugriffssteuerungstypen für Clients, die mit einem Service Fabric-Cluster verbunden sind:

-   **Administrator**: Vollzugriff auf Verwaltungsfunktionen (einschließlich Lese-/Schreibzugriff).
-   **User:** Nur Lesezugriff auf Verwaltungsfunktionen (z.B. Abfragefunktionen) sowie die Möglichkeit, Anwendungen und Dienste aufzulösen.

Mithilfe der Zugriffssteuerung können Clusteradministratoren den Zugriff auf bestimmte Arten von Clustervorgängen beschränken. Dadurch wird der Cluster sicherer.

#### <a name="client-to-node-certificate-security"></a>Zertifikatsicherheit (Client zu Knoten)

Sie konfigurieren die Client-zu-Knoten-Zertifikatsicherheit beim Erstellen eines Clusters über das Azure-Portal, über Azure Resource Manager-Vorlagen oder über eine eigenständige JSON-Vorlage. Sie müssen ein Clientzertifikat für einen Administrator und/oder einen Benutzer angeben. Stellen Sie sicher, dass sich diese Zertifikate von der primären und sekundären Zertifikaten unterscheiden, die Sie für die Knoten-zu-Knoten-Sicherheit angeben.

Clients, die unter Verwendung des Administratorzertifikats eine Verbindung mit dem Cluster herstellen, haben uneingeschränkten Zugriff auf die Verwaltungsfunktionen. Clients, die unter Verwendung des schreibgeschützten Benutzerclientzertifikats eine Verbindung mit dem Cluster herstellen, haben nur Lesezugriff auf die Verwaltungsfunktionen. Diese Zertifikate werden also für die rollenbasierte Zugriffssteuerung (Role-based Access Control, RBAC) verwendet.

Informationen zum Konfigurieren der Zertifikatsicherheit in einem Cluster finden Sie unter [Einrichten eines Clusters mit einer Azure Resource Manager-Vorlage](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Client-zu-Knoten-Sicherheit von Azure Active Directory in Azure

In Azure ausgeführte Cluster können den Zugriff auf die Verwaltungsendpunkte auch mit Azure Active Directory (Azure AD) schützen. Unter [Einrichten eines Clusters mit einer Azure Resource Manager-Vorlage](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) erfahren Sie, wie Sie die erforderlichen Azure Active Directory-Artefakte erstellen, sie während der Clustererstellung auffüllen und danach die Verbindung mit diesen Clustern herstellen.

Mit Azure AD können Organisationen (so genannte Mandanten) den Benutzerzugriff auf Anwendungen verwalten. Es gibt Anwendungen mit webbasierter Anmeldebenutzeroberfläche und Anwendungen mit nativer Clientoberfläche.

Service Fabric-Cluster bieten unterschiedliche Einstiegspunkte für ihre Verwaltungsfunktionen. Hierzu zählen etwa der webbasierte Service Fabric Explorer und Visual Studio. Daher erstellen Sie zwei Azure AD-Anwendungen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung.

Für Azure-Cluster wird die Verwendung der Azure AD-Sicherheit empfohlen, um Clients und Zertifikate für die Knoten-zu-Knoten-Sicherheit zu authentifizieren.

Für eigenständige Windows Server-Cluster mit Windows Server 2012 R2 und Active Directory wird die Verwendung der Windows-Sicherheit mit gruppenverwalteten Dienstkonten empfohlen. Verwenden Sie andernfalls die Windows-Sicherheit mit Windows-Konten.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Grundlegendes zur Überwachung und Diagnose in Azure Service Fabric
[Überwachung und Diagnose](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sind wichtig für Entwicklung und Tests sowie die Bereitstellung von Anwendungen und Diensten in allen Umgebungen. Service Fabric-Lösungen funktionieren am besten, wenn Sie die Überwachung und Diagnose implementieren. So stellen Sie sicher, dass Anwendungen und Dienste in einer lokalen Entwicklungsumgebung oder in der Produktionsumgebung wie erwartet ausgeführt werden.

Mit Blick auf Sicherheit sind die Hauptziele der Überwachung und Diagnose die folgenden:

-   Erkennen und Diagnostizieren von Hardware- und Infrastrukturproblemen, die auf ein Sicherheitsereignis zurückzuführen sein können.
-   Erkennen von Software- und App-Problemen, die einen Hinweis auf eine Gefährdung (Indicator of Compromise, IoC) darstellen können.
-   Nachvollziehen des Ressourcenverbrauchs zur Verhinderung unbeabsichtigter Denial-of-Service-Angriffe.

Der Workflow für die Überwachung und Diagnose besteht aus drei Schritten:

1.  **Ereignisgenerierung**: Die Ereignisgenerierung schließt Ereignisse (Protokolle, Ablaufverfolgungen, benutzerdefinierte Ereignisse) auf Infrastruktur- (Cluster-) und Anwendungs-/Dienstebene ein. Weitere Informationen zu den enthaltenen Instrumentierungen und zum Hinzufügen weiterer Instrumentierungen finden Sie unter [Ereignisse auf Infrastrukturebene](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) und [Ereignisse auf Anwendungsebene](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).

2.  **Ereignisaggregation**: Die generierten Ereignisse müssen gesammelt und aggregiert werden, bevor sie angezeigt werden können. In der Regel empfehlen wir die Verwendung von [Azure-Diagnose](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (ähnlich der Agent-basierten Protokollsammlung) oder [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (In-Process-Protokollsammlung).

3.  **Analyse**: Ereignisse müssen visualisiert und in einem Format verfügbar sein, das die Analyse und Anzeige ermöglicht. Es gibt mehrere Plattformen für die Analyse und Visualisierung von Überwachungs- und Diagnosedaten. Aufgrund ihrer guten Integration in Service Fabric werden [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) und [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) empfohlen.

Sie können auch [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) zum Überwachen von vielen Azure-Ressourcen verwenden, aus denen ein Service Fabric-Cluster erstellt wird.

Ein Watchdog ist ein separater Dienst, der die Integrität und die Auslastung von Diensten überwachen und die Integrität für alle Elemente in der Hierarchie des Integritätsmodells melden kann. Mithilfe eines Watchdogs können Fehler verhindert werden, die nicht erkannt werden, wenn nur ein einzelner Dienst betrachtet wird. 

Watchdogs sind auch eine gute Möglichkeit zum Hosten von Code, der Aktionen zur Problembehebung ohne Eingreifen des Benutzers ausführt. Ein Beispiel ist de Bereinigung von Protokolldateien im Speicher in bestimmten Zeitabständen. Im [Beispiel für den Azure Service Fabric-Watchdog](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/) finden Sie eine Beispielimplementierung eines Watchdog-Diensts.

## <a name="secure-communication-by-using-certificates"></a>Grundlegendes zum Schützen der Kommunikation mithilfe von Zertifikaten
Zertifikate können Sie die Kommunikation zwischen den verschiedenen Knoten Ihres eigenständigen Windows-Clusters schützen helfen. Mithilfe von X.509-Zertifikaten können Sie auch Clients authentifizieren, die sich mit diesem Cluster verbinden. Dadurch wird sichergestellt, dass nur autorisierte Benutzer Zugriff auf den Cluster haben. Es wird empfohlen, ein Zertifikat bei seiner Erstellung für den Cluster zu aktivieren.

Digitale X.509-Zertifikate werden in der Regel verwendet, um Clients und Server zu authentifizieren. Sie werden auch zum Verschlüsseln und digitalen Signieren von Nachrichten verwendet.

In der folgenden Tabelle sind die Zertifikate aufgeführt, die Sie für die Einrichtung des Clusters benötigen:

|Zertifikatinformationseinstellung |BESCHREIBUNG|
|-------------------------------|-----------|
|ClusterCertificate|    Dieses Zertifikat ist erforderlich, um die Kommunikation zwischen den Knoten in einem Cluster zu schützen. Sie können zwei Clusterzertifikate verwenden: ein primäres Zertifikat und ein sekundäres Zertifikat für Upgrades.|
|ServerCertificate| Dieses Zertifikat wird dem Client angezeigt, wenn versucht wird, eine Verbindung mit diesem Cluster herzustellen. Sie können zwei Serverzertifikate verwenden: ein primäres Zertifikat und ein sekundäres Zertifikat für Upgrades.|
|ClientCertificateThumbprints|  Dies ist eine Gruppe von Zertifikaten, die auf den authentifizierten Clients installiert werden sollen.|
|ClientCertificateCommonNames|  Dies ist der allgemeine Name des ersten Clientzertifikats für „CertificateCommonName“. „CertificateIssuerThumbprint“ ist der Fingerabdruck für den Aussteller dieses Zertifikats.|
|ReverseProxyCertificate|   Hierbei handelt es sich um ein optionales Zertifikat, das Sie zum Schutz des [Reverseproxys](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) angeben können.|

Weitere Informationen zum Schützen von Zertifikaten finden Sie unter [Schützen eines eigenständigen Clusters unter Windows mithilfe von X.509-Zertifikaten](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Grundlegendes zur rollenbasierten Zugriffssteuerung
Sie geben die Administrator- und die Benutzerclientrolle zum Zeitpunkt der Clustererstellung an, indem Sie jeweils separate Identitäten (einschließlich Zertifikaten) bereitstellen. Weitere Informationen zu den Standardeinstellungen der Zugriffssteuerung sowie zum Ändern der Standardeinstellungen finden Sie unter [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Schützen von eigenständigen Clustern mit Windows-Sicherheit
Um nicht autorisierten Zugriff auf einen Service Fabric-Cluster zu verhindern, müssen Sie den Cluster schützen. Sicherheit ist besonders wichtig, wenn der Cluster Produktionsworkloads ausführt. Sie konfigurieren die Knoten-zu-Knoten- und Client-zu-Knoten-Sicherheit mit Windows-Sicherheit in der Datei „ClusterConfig.JSON“.

Die Knoten-zu-Knoten-Sicherheit konfigurieren Sie durch Festlegen von [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security), wenn Service Fabric unter einem gruppenverwalteten Dienstkonto (Group-Managed Service Account, gMSA) ausgeführt werden muss. Um Vertrauensstellungen zwischen Knoten einrichten zu können, müssen Sie sie über das Vorhandensein des jeweils anderen Knotens informieren.

Wenn Sie eine Computergruppe in einer Active Directory-Domäne verwenden möchten, wird die Knoten-zu-Knoten-Sicherheit durch Festlegen von „ClusterIdentity“ konfiguriert. Weitere Informationen finden Sie unter [Erstellen einer Computergruppe in Active Directory](https://msdn.microsoft.com/library/aa545347).

Sie konfigurieren die Client-zu-Knoten-Sicherheit mithilfe von „ClientIdentities“. Sie müssen den Cluster so konfigurieren, dass erkannt wird, welche Clientidentitäten vertrauenswürdig sind. Sie können Vertrauensstellungen auf zwei Arten erstellen:

-   Geben Sie die Domänengruppenbenutzer an, die eine Verbindung herstellen können.
-   Geben Sie die Domänenknotenbenutzer an, die eine Verbindung herstellen können.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurieren der Anwendungssicherheit in Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Verwalten von Geheimnissen in Service Fabric-Anwendungen
Geheimnisse beinhalten jegliche Art von vertraulichen Informationen (z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen).

In diesem Leitfaden wird [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) für die Verwaltung von Schlüsseln und Geheimnissen verwendet. Allerdings ist die Verwendung von Geheimnissen in einer Anwendung nicht von einer bestimmten Cloud-Plattform abhängig. Sie können Anwendungen in einem Cluster bereitstellen, der an einem beliebigen Ort gehostet wird. Dieser Vorgang besteht im Wesentlichen aus vier Schritten:

1.  Rufen Sie ein Datenverschlüsselungszertifikat ab.
2.  Installieren des Zertifikats in Ihrem Cluster
3.  Verschlüsseln von Geheimnissen bei der Bereitstellung einer Anwendung mit dem Zertifikat und Einfügen dieser Geheimnisse in die Konfigurationsdatei „Settings.xml“ des Diensts
4.  Lesen der verschlüsselten Werte aus der Datei „Settings.xml“, indem diese mit demselben Verschlüsselungszertifikat entschlüsselt werden

Weitere Informationen finden Sie im Artikel [Verwalten von Geheimnissen in Service Fabric-Anwendungen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Konfigurieren von Sicherheitsrichtlinien für eine Anwendung
Durch Verwenden der Azure Service Fabric-Sicherheit können Sie Anwendungen sichern, die im Cluster mit verschiedenen Benutzerkonten ausgeführt werden. Mit der Service Fabric-Sicherheit werden auch die Ressourcen gesichert, die von Anwendungen zum Zeitpunkt der Bereitstellung in den Benutzerkonten verwendet werden, z.B. Dateien, Verzeichnisse und Zertifikate. Dadurch wird das Ausführen von Anwendungen, selbst in einer gemeinsam genutzten gehosteten Umgebung, sicherer.

Aufgaben zum Konfigurieren von Sicherheitsrichtlinien:

-   Konfigurieren der Richtlinie für einen Setupeinstiegspunkt für Dienste
-   Starten von PowerShell-Befehlen über einen Setupeinstiegspunkt
-   Verwenden der Konsolenumleitung für das lokale Debuggen
-   Konfigurieren einer Richtlinie für Dienstcodepakete
-   Zuweisen einer Sicherheitszugriffsrichtlinie für HTTP- und HTTPS-Endpunkte

## <a name="secure-communication-for-services"></a>Sichern der Kommunikation für Dienste
Sicherheit ist einer der wichtigsten Aspekte der Kommunikation. Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. Weitere Informationen finden Sie unter [Schützen der Dienstremotingkommunikation für einen Dienst](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Nächste Schritte
- Konzeptionelle Informationen zur Clustersicherheit finden Sie unter [Erstellen eines Service Fabric-Clusters mithilfe von Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) und im [Create a Service Fabric cluster by using the Azure portal (Erstellen eines Service Fabric-Clusters mithilfe des Azure-Portals)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Weitere Informationen zur Clustersicherheit in Service Fabric finden Sie unter [Szenarios für die Clustersicherheit in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
