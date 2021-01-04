---
title: Bewährte Methoden für die Azure Service Fabric-Sicherheit
description: Dieser Artikel enthält bewährte Methoden für die Azure Service Fabric-Sicherheit.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7396c9a29c7d9f69dbe6a9cc5cd085c72ebafde
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700945"
---
# <a name="azure-service-fabric-security-best-practices"></a>Bewährte Methoden für die Azure Service Fabric-Sicherheit
Die Bereitstellung einer Anwendung in Azure ist schnell, einfach und kostengünstig. Bevor Sie Ihre Cloudanwendung für die Produktion bereitstellen, überprüfen Sie unsere Liste grundlegender und empfohlener bewährter Methoden zum Implementieren sicherer Cluster in der Anwendung.

Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht. Service Fabric bietet außerdem einfache Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von Cloudanwendungen. Entwickler und Administratoren können komplexe Infrastrukturprobleme vermeiden und sich auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind.

Für jede bewährte Methode wird Folgendes beschrieben:

-   Wobei es bei der bewährten Methode geht
-   Gründe für die Umsetzung der bewährten Methode
-   Mögliche Folgen, wenn Sie die bewährte Methode nicht implementieren
-   Anleitungen für die Umsetzung der bewährten Methode

Wir empfehlen die folgenden bewährten Methoden für die Azure Service Fabric-Sicherheit:

-   Verwenden von Azure Resource Manager-Vorlagen und des PowerShell-Moduls für Service Fabric zum Erstellen sicherer Cluster
-   Verwenden von X.509-Zertifikaten
-   Konfigurieren von Sicherheitsrichtlinien
-   Implementieren der Reliable Actors-Sicherheitskonfiguration
-   Konfigurieren von TLS für Azure Service Fabric
-   Verwenden von Netzwerkisolation und -sicherheit mit Azure Service Fabric
-   Konfigurieren von Azure Key Vault für die Sicherheit
-   Zuweisen von Benutzern zu Rollen


## <a name="best-practices-for-securing-your-clusters"></a>Bewährte Methoden zum Schützen der Cluster

Verwenden Sie immer einen sicheren Cluster:
-   Implementieren Sie die Clustersicherheit mithilfe von Zertifikaten.
-   Stellen Sie den Clientzugriff (Administrator und schreibgeschützt) mithilfe von Azure Active Directory (Azure AD) bereit.

Verwenden Sie automatische Bereitstellungen:
-   Verwenden Sie Skripts zum Generieren, Bereitstellen und Ausführen eines Rollovers von Geheimnissen.
-   Speichern Sie die Geheimnisse in Azure Key Vault, und verwenden Sie Azure AD für alle anderen Clientzugriffe.
-   Erzwingen Sie eine Authentifizierung für den Benutzerzugriff auf die Geheimnisse.

Berücksichtigen Sie außerdem folgende Konfigurationsoptionen:
-   Erstellen Sie mithilfe von Azure-Netzwerksicherheitsgruppen (NSGs) Umkreisnetzwerke (auch bekannt als demilitarisierte Zonen, DMZs, oder überwachte Subnetze).
-   Verwenden Sie für den Zugriff auf die virtuellen Computer (VMs) im Cluster oder die Verwaltung Ihres Clusters Sprungserver mit Remotedesktopverbindung.

Ihre Cluster müssen geschützt werden, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung damit herstellen können. Dies gilt insbesondere dann, wenn der Cluster in der Produktionsumgebung ausgeführt wird. Es ist zwar möglich, einen ungeschützten Cluster zu erstellen, doch falls der Cluster Verwaltungsendpunkte im öffentlichen Internet verfügbar macht, können anonyme Benutzer eine Verbindung mit ihm herstellen.

Es gibt drei [Szenarien](../../service-fabric/service-fabric-cluster-security.md) für das Implementieren von Clustersicherheit mithilfe verschiedener Technologien:

-   Knoten-zu-Knoten-Sicherheit: Mit diesem Szenario wird die Kommunikation zwischen den VMs und den Computern im Cluster geschützt. So wird sichergestellt, dass nur Computer, die zum Beitreten zum Cluster berechtigt sind, Anwendungen und Dienste im Cluster hosten können.
In diesem Szenario können in Azure ausgeführte Cluster oder eigenständige unter Windows ausgeführte Cluster für Windows Server-Computer entweder die [Zertifikatsicherheit](../../service-fabric/service-fabric-windows-cluster-x509-security.md) oder die [Windows-Sicherheit](../../service-fabric/service-fabric-windows-cluster-windows-security.md) verwenden.
-   Client-zu-Knoten-Sicherheit: Mit diesem Szenario wird die Kommunikation zwischen einem Service Fabric-Client und den einzelnen Knoten im Cluster geschützt.
-   Rollenbasierte Zugriffssteuerung in Service Fabric (Service Fabric RBAC): In diesem Szenario werden separate Identitäten (Zertifikate, Azure AD usw.) für alle Administrator- und Clientrollen verwendet, die auf den Cluster zugreifen. Sie geben die Rollenidentitäten bei der Erstellung des Clusters an.

>[!NOTE]
>**Sicherheitsempfehlung für Azure-Cluster:** Verwenden Sie Azure AD-Sicherheit, um Clients und Zertifikate für die Knoten-zu-Knoten-Sicherheit zu authentifizieren.

Informationen zum Konfigurieren eines eigenständigen Windows-Clusters finden Sie unter [Konfigurationseinstellungen für eigenständige Windows-Cluster](../../service-fabric/service-fabric-cluster-manifest.md).

Verwenden Sie Azure Resource Manager-Vorlagen und das PowerShell-Modul für Service Fabric zum Erstellen eines sicheren Clusters.
Eine ausführliche Anleitung zum Erstellen eines Service Fabric-Clusters mit Azure Resource Manager-Vorlagen finden Sie unter [Erstellen eines Service Fabric-Clusters](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Verwenden Sie die Azure Resource Manager-Vorlage:
-   Passen Sie Ihren Cluster mithilfe der Vorlage an, um den verwalteten Speicher für virtuelle Festplatten (VHDs) der virtuellen Computer zu konfigurieren.
-   Übertragen Sie Ihre Änderungen für die einfache Verwaltung und Überwachung der Konfiguration mithilfe der Vorlage an Ihre Ressourcengruppe.

Behandeln Sie die Clusterkonfiguration wie Code:
-   Überprüfen Sie Ihre Bereitstellungskonfigurationen besonders gründlich.
-   Vermeiden Sie die Verwendung impliziter Befehle, um Ihre Ressourcen direkt zu ändern.

Viele Aspekte des [Service Fabric-Anwendungslebenszyklus](../../service-fabric/service-fabric-application-lifecycle.md) können automatisiert werden. Das [PowerShell-Modul für Service Fabric](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatisiert allgemeine Aufgaben zum Bereitstellen, Aktualisieren, Entfernen und Testen von Azure Service Fabric-Anwendungen. Verwaltete APIs und HTTP-APIs für die Anwendungsverwaltung sind ebenfalls verfügbar.

## <a name="use-x509-certificates"></a>Verwenden von X.509-Zertifikaten
Cluster sollten immer mit X.509-Zertifikaten oder Windows-Sicherheit geschützt werden. Die Sicherheit wird nur zum Zeitpunkt der Clustererstellung konfiguriert. Es ist nicht möglich, die Sicherheit nach dem Erstellen eines Clusters zu aktivieren.

Wenn Sie ein [Clusterzertifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md) angeben, legen Sie den Wert der **ClusterCredentialType**-Eigenschaft auf „X509“ fest. Wenn Sie ein Serverzertifikat für externe Verbindungen angeben, legen Sie die **ServerCredentialType**-Eigenschaft auf „X509“ fest.

Wenden Sie darüber hinaus diese empfohlenen Methoden an:
-   Erstellen Sie die Zertifikate für Produktionscluster mit einem ordnungsgemäß konfigurierten Windows Server-Zertifikatdienst. Sie können die Zertifikate auch von einer genehmigten Zertifizierungsstelle (CA) abrufen.
-   Verwenden Sie niemals ein temporäres oder Testzertifikat für Produktionscluster, wenn das Zertifikat mit „MakeCert.exe“ oder einem ähnlichen Tool erstellt wurde.
-   Selbstsignierte Zertifikate können für Testcluster, aber nicht für Produktionscluster verwendet werden.

Ist der Cluster unsicher, kann jeder Benutzer anonym eine Verbindung herstellen und Verwaltungsvorgänge ausführen. Aus diesem Grund sollten Produktionscluster immer mit X.509-Zertifikaten oder Windows-Sicherheit geschützt werden.

Weitere Informationen zur Verwendung von X.509-Zertifikaten finden Sie unter [Hinzufügen oder Entfernen von Zertifikaten für einen Service Fabric-Cluster](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurieren von Sicherheitsrichtlinien
Service Fabric schützt auch Ressourcen, die von Anwendungen verwendet werden. Ressourcen wie Dateien, Verzeichnisse und Zertifikate werden in den Benutzerkonten gespeichert, wenn die Anwendung bereitgestellt wird. Mit diesem Feature wird erreicht, dass ausgeführte Anwendungen auch in einer gemeinsamen gehosteten Umgebung sicher voneinander abgegrenzt sind.

-   Verwenden einer Active Directory-Domänengruppe oder eines Active Directory-Benutzers: Führen Sie den Dienst mit den Anmeldeinformationen für einen Active Directory-Benutzer oder ein Active Directory-Gruppenkonto aus. Verwenden Sie unbedingt eine lokale Active Directory-Instanz in Ihrer Domäne, nicht Azure Active Directory. Auf andere Ressourcen in der Domäne greifen Sie über einen Domänenbenutzer oder eine Domänengruppe zu, für den bzw. die Berechtigungen gewährt wurden. Zu solchen Ressourcen gehören z.B. Dateifreigaben.

-   Zuweisen einer Sicherheitszugriffsrichtlinie für HTTP- und HTTPS-Endpunkte: Geben Sie die **SecurityAccessPolicy**-Eigenschaft an, um eine **RunAs**-Richtlinie auf einen Dienst anzuwenden, wenn das Dienstmanifest Endpunktressourcen mit HTTP deklariert. Ports, die den HTTP-Endpunkten zugeordnet sind, werden ordnungsgemäß über Zugriffssteuerungslisten für das RunAs-Benutzerkonto, unter dem der Dienst ausgeführt wird, gesteuert. Wurde die Richtlinie nicht festgelegt, hat „http.sys“ keinen Zugriff auf den Dienst, sodass bei Aufrufen vom Client Fehler auftreten können.

Weitere Informationen zum Verwenden von Sicherheitsrichtlinien in Service Fabric-Clustern finden Sie unter [Konfigurieren von Sicherheitsrichtlinien für Ihre Anwendung](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementieren der Reliable Actors-Sicherheitskonfiguration
Service Fabric Reliable Actors ist eine Implementierung des Akteurentwurfsmusters. Die Entscheidung, ob ein bestimmtes Muster verwendet werden soll, wird basierend darauf getroffen, ob ein Softwareproblem dem Muster entspricht.

Im Allgemeinen verwenden Sie das Akteurentwurfsmuster, um Modelllösungen für die folgenden Softwareprobleme oder Sicherheitsszenarien zu unterstützen:
-   Der Problembereich enthält zahlreiche (mehrere Tausend) kleine, unabhängige und isolierte Zustands- und Logikeinheiten.
-   Sie arbeiten mit Singlethread-Objekten, die keine wesentliche Interaktion von externen Komponenten erfordern (etwa, um den Zustand für einen Satz von Akteuren abzufragen).
-   Ihre Akteurinstanzen blockieren Aufrufer durch die Ausgabe von E/A-Vorgängen nicht mit unvorhersagbaren Verzögerungen.

In Service Fabric werden Akteure im Reliable Actors-Anwendungsframework implementiert. Dieses Framework basiert auf dem Akteurmuster und baut auf [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md) auf. Jeder von Ihnen geschriebene Reliable Actors-Dienst ist eine partitionierte, zustandsbehaftete Reliable Services-Instanz.

Jeder Akteur wird als Instanz eines Akteurtyps definiert (genau wie ein .NET-Objekt eine Instanz eines .NET-Typs darstellt). Beispielsweise kann ein **Akteurtyp** die Funktionalität eines Rechners implementieren, und es kann viele Akteure dieses Typs geben, die auf verschiedene Knoten in einem Cluster verteilt sind. Jeder der verteilten Akteure ist durch einen Akteursbezeichner eindeutig gekennzeichnet.

[Replicator-Sicherheitskonfigurationen](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) werden verwendet, um den während der Replikation verwendeten Kommunikationskanal zu schützen. Diese Konfiguration verhindert, dass Dienste den Replikationsdatenverkehr anderer Dienste sehen können, und sie stellt sicher, dass die hoch verfügbaren Daten sicher sind. Standardmäßig wird die Replikationssicherheit durch einen leeren Sicherheitskonfigurationsabschnitt verhindert.
Replicator-Konfigurationen konfigurieren den Replicator, der dafür verantwortlich ist, den Status des Actor-Status-Anbieters hochverfügbar zu machen.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurieren von TLS für Azure Service Fabric
Der Serverauthentifizierungsprozess [authentifiziert](../../service-fabric/service-fabric-cluster-creation-via-arm.md) die Verwaltungsendpunkte des Clusters bei einem Verwaltungsclient. Der Verwaltungsclient erkennt damit, dass er mit dem tatsächlichen Cluster kommuniziert. Dieses Zertifikat stellt auch [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) für die HTTPS-Verwaltungs-API und für Service Fabric Explorer über HTTPS bereit.
Sie benötigen einen benutzerdefinierten Domänennamen für Ihren Cluster. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat dem benutzerdefinierten Domänennamen entsprechen, den Sie für Ihren Cluster verwenden.

Wenn Sie für eine Anwendung TLS konfigurieren möchten, müssen Sie zunächst ein TLS-Zertifikat abrufen, das von einer Zertifizierungsstelle signiert wurde. Die Zertifizierungsstelle ist eine vertrauenswürdige dritte Partei, die Zertifikate für die TLS-Sicherheit ausstellt. Wenn Sie noch kein SSL-/TLS-Zertifikat besitzen, müssen Sie ein Zertifikat von einem Unternehmen erwerben, das SSL-/TLS-Zertifikate verkauft.

Das Zertifikat muss die folgenden Anforderungen für SSL-/TLS-Zertifikate in Azure erfüllen:
-   Das Zertifikat muss einen privaten Schlüssel enthalten.

-   Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.

-   Der Name des Antragstellers für das Zertifikat muss dem Domänennamen entsprechen, über den auf den Clouddienst zugegriffen wird.

    - Beschaffen Sie einen benutzerdefinierten Domänennamen, den Sie für den Zugriff auf Ihren Clouddienst verwenden können.
    - Verwenden Sie beim Anfordern eines Zertifikats von einer Zertifizierungsstelle einen Antragstellernamen, der dem benutzerdefinierte Domänenname des Diensts entspricht. Wenn beispielsweise der benutzerdefinierte Domänenname __contoso__ **.com** lautet, sollte das Zertifikat von der Zertifizierungsstelle den Antragstellernamen **.contoso.com** oder __www__ **.contoso.com** haben.

    >[!NOTE]
    >Für die Domäne __cloudapp__ **.net** können Sie kein SSL-/TLS-Zertifikat von einer Zertifizierungsstelle beziehen.

-   Das Zertifikat muss mindestens eine 2.048-Bit-Verschlüsselung aufweisen.

Das HTTP-Protokoll ist unsicher und anfällig für Lauschangriffe. Daten, die über HTTP übertragen werden, werden als Klartext vom Webbrowser an den Webserver oder zwischen anderen Endpunkten übertragen. Dies bedeutet, dass Angreifer sensible Daten, z.B. Kreditkartendaten und Anmeldedaten für Konten, die über HTTP übermittelt werden, abfangen und anzeigen können. Wenn die Daten mithilfe von HTTPS über einen Browser gesendet oder bereitgestellt werden, wird mit SSL sichergestellt, dass vertrauliche Informationen verschlüsselt sind und nicht abgefangen werden können.

Weitere Informationen zur Verwendung von SSL-/TLS-Zertifikaten finden Sie unter [Konfigurieren von TLS für eine Anwendung in Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Verwenden von Netzwerkisolation und -sicherheit mit Azure Service Fabric
Richten Sie als Beispiel einen sicheren Cluster mit 3 Knoten mithilfe der [Azure Resource Manager-Vorlage](../../azure-resource-manager/templates/template-syntax.md) ein. Sie steuern den ein- und ausgehenden Netzwerkdatenverkehr mithilfe der Vorlage und von Netzwerksicherheitsgruppen.

Die Vorlage weist eine Netzwerksicherheitsgruppe für jede VM-Skalierungsgruppe auf und dient der Steuerung des Datenverkehrs in der Gruppe und aus dieser heraus. Standardmäßig werden die Regeln so konfiguriert, dass der gesamte von den Systemdiensten benötigte Datenverkehr und die in der Vorlage angegebenen Anwendungsports zugelassen werden. Überprüfen Sie diese Regeln, und ändern Sie sie nach Ihren Bedürfnissen, indem Sie z.B. neue Regeln für Ihre Anwendungen hinzufügen.

Weitere Informationen finden Sie unter [Netzwerkmuster für Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Einrichten einer Azure Key Vault-Instanz für die Sicherheit
Zertifikate werden in Service Fabric zur Authentifizierung und Verschlüsselung verwendet, um Cluster und die zugehörigen Anwendungen zu schützen.

Service Fabric verwendet X.509-Zertifikate, um einen Cluster zu schützen und Sicherheitsfunktionen für Anwendungen bereitzustellen. Azure Key Vault dient dem [Verwalten von Zertifikaten](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) für Service Fabric-Cluster in Azure. Der Azure-Ressourcenanbieter, der die Cluster erstellt, ruft die Zertifikate aus einem Schlüsseltresor ab. Der Anbieter installiert die Zertifikate auf den virtuellen Computern, wenn der Cluster in Azure bereitgestellt wird.

Eine Zertifikatbeziehung besteht zwischen [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), dem Service Fabric-Cluster und dem Ressourcenanbieter, der die Zertifikate verwendet. Wenn der Cluster erstellt wird, werden Informationen über die Zertifikatbeziehung in einem Schlüsseltresor gespeichert.

Das Einrichten eines Schlüsseltresors umfasst zwei grundlegende Schritte:
1. Erstellen Sie eine neue Ressourcengruppe speziell für Ihren Schlüsseltresor.

    Es empfiehlt sich, den Schlüsseltresor in einer eigenen Ressourcengruppe zu platzieren. Dies verhindert einen Verlust Ihrer Schlüssel und Geheimnisse, wenn andere Ressourcengruppen wie Speicher- oder Computeressourcen oder die Gruppe, die den Cluster enthält, entfernt werden. Die Ressourcengruppe mit Ihrem Schlüsseltresor muss sich in der gleichen Region befinden wie der Cluster, der den Tresor verwendet.

2. Erstellen Sie einen Schlüsseltresor in der neuen Ressourcengruppe.

    Der Schlüsseltresor muss für die Bereitstellung aktiviert sein. Der Computeressourcenanbieter kann dann die Zertifikate vom Tresor abrufen und auf den VM-Instanzen installieren.

Weitere Informationen zum Einrichten eines Schlüsseltresors finden Sie unter [Was ist Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Zuweisen von Benutzern zu Rollen
Nachdem Sie die Anwendungen für Ihren Cluster erstellt haben, müssen Ihre Benutzer den von Service Fabric unterstützten Rollen zugewiesen werden: „read-only“ (schreibgeschützt) und „admin“ (Administrator). Für die Rollenzuweisung können Sie das Azure-Portal verwenden.

>[!NOTE]
> Weitere Informationen zur Verwendung von Rollen in Service Fabric finden Sie unter [Rollenbasierte Zugriffssteuerung in Service Fabric für Service Fabric-Clients](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric unterstützt zwei Zugriffssteuerungstypen für Clients, die mit einem [Service Fabric-Cluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md) verbunden sind: Administrator und Benutzer. Mit der Zugriffssteuerung können Clusteradministratoren den Zugriff auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern einschränken. Diese Zugriffssteuerung macht den Cluster sicherer.

## <a name="next-steps"></a>Nächste Schritte

- [Checkliste für die Service Fabric-Sicherheit](../../service-fabric/service-fabric-best-practices-security.md)
- Richten Sie Ihre Service Fabric-[Entwicklungsumgebung](../../service-fabric/service-fabric-get-started.md) ein.
- Informieren Sie sich über [Service Fabric-Supportoptionen](../../service-fabric/service-fabric-support.md).