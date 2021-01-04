---
title: Sichern eines Azure Service Fabric-Clusters
description: Lernen Sie Sicherheitsszenarien für einen Azure Service Fabric-Cluster und die verschiedenen Technologien kennen, die Sie verwenden können, um sie zu implementieren.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573067"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Szenarien für die Clustersicherheit in Service Fabric

Ein Azure Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet. Sie müssen Ihre Cluster schützen, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung mit ihnen herstellen. Ein sicherer Cluster ist besonders wichtig, wenn Sie Produktionsworkloads im Cluster ausführen. Es ist möglich, einen ungeschützten Cluster zu erstellen, doch falls der Cluster Verwaltungsendpunkte im öffentlichen Internet bereitstellt, können anonyme Benutzer eine Verbindung mit ihm herstellen. Nicht geschützte Cluster werden für Produktionsworkloads nicht unterstützt. 

Dieser Artikel bietet eine Übersicht über Sicherheitsszenarien für Azure-Cluster und eigenständige Cluster sowie die verschiedenen Technologien, die Sie zu deren Implementierung verwenden können:

* Knoten-zu-Knoten-Sicherheit
* Client-zu-Knoten-Sicherheit
* Rollenbasierte Service Fabric-Zugriffssteuerung

## <a name="node-to-node-security"></a>Knoten-zu-Knoten-Sicherheit

Die Knoten-zu-Knoten-Sicherheit dient zum Schutz der Kommunikation zwischen den virtuellen oder physischen Computern in einem Cluster. Dieses Sicherheitsszenario stellt sicher, dass nur Computer, die zum Clusterbeitritt berechtigt sind, Anwendungen und Dienste im Cluster hosten können.

![Diagramm für die Kommunikation zwischen Knoten][Node-to-Node]

Cluster unter Azure und eigenständige Cluster unter Windows können beide entweder [Zertifikatsicherheit](/previous-versions/msp-n-p/ff649801(v=pandp.10)) oder [Windows-Sicherheit](/previous-versions/msp-n-p/ff649396(v=pandp.10)) für Windows Server-Computer verwenden.

### <a name="node-to-node-certificate-security"></a>Zertifikatsicherheit (Knoten zu Knoten)

Service Fabric verwendet X.509-Serverzertifikate, die Sie als Teil der Knotentypkonfiguration angeben, wenn Sie einen Cluster erstellen. Ein kurzer Überblick darüber, was diese Zertifikate sind und wie Sie sie abrufen oder erstellen können, wird am Ende dieses Artikels bereitgestellt.

Richten Sie die Zertifikatsicherheit beim Erstellen des Clusters im Azure-Portal, über Azure Resource Manager-Vorlagen oder eine eigenständige JSON-Vorlage ein. Das Standardverhalten des Service Fabric SDK ist die Bereitstellung und Installation des Zertifikats mit dem am weitesten in der Zukunft liegenden Ablaufdatum. Das klassische Verhalten erlaubt die Definition von primären und sekundären Zertifikaten, um manuell initiierte Rollover zu ermöglichen, und wird nicht für die Verwendung mit der neuen Funktionalität empfohlen. Die primären Zertifikate, die verwendet werden, weisen das am weitesten in die Zukunft reichende Ablaufdatum auf und sollten sich vom Verwaltungsclient und den schreibgeschützten Clientzertifikaten unterscheiden, die Sie für [Client-zu-Knoten-Sicherheit](#client-to-node-security) festgelegt haben.

Informationen zum Einrichten der Zertifikatsicherheit in einem Cluster für Azure finden Sie unter [Einrichten eines Clusters mit einer Azure Resource Manager-Vorlage](service-fabric-cluster-creation-via-arm.md).

Informationen zum Einrichten der Zertifikatsicherheit in einem Cluster für einen eigenständigen Windows Server-Cluster finden Sie unter [Schützen eines eigenständigen Windows-Clusters mithilfe von X.509-Zertifikaten](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Windows-Sicherheit (Knoten zu Knoten)

> [!NOTE]
> Die Windows-Authentifizierung basiert auf Kerberos. NTLM wird als Authentifizierungstyp nicht unterstützt.
>
> Verwenden Sie nach Möglichkeit die X.509-Zertifikatauthentifizierung für Service Fabric-Clusters.

Informationen zum Einrichten der Windows-Sicherheit für einen eigenständigen Windows Server-Cluster finden Sie unter [Schützen eines eigenständigen Windows-Clusters mit Windows-Sicherheit](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Client-zu-Knoten-Sicherheit

Client-zu-Knoten-Sicherheit authentifiziert Clients und schützt die Kommunikation zwischen einem Client und einzelnen Knoten im Cluster. Diese Art von Sicherheit sorgt dafür, dass nur autorisierte Benutzer auf den Cluster und die im Cluster bereitgestellten Anwendungen zugreifen können. Clients werden entweder über ihre Windows-Sicherheitsanmeldeinformationen oder ihre Zertifikat-Sicherheitsanmeldeinformationen eindeutig identifiziert.

![Diagramm für die Kommunikation zwischen Client und Knoten][Client-to-Node]

Sowohl in Azure ausgeführte Cluster als auch eigenständige Cluster unter Windows können entweder die [Zertifikatsicherheit](/previous-versions/msp-n-p/ff649801(v=pandp.10)) oder die [Windows-Sicherheit](/previous-versions/msp-n-p/ff649396(v=pandp.10)) verwenden. Empfohlen wird jedoch die Verwendung der X.509-Zertifikatauthentifizierung, wann immer dies möglich ist.

### <a name="client-to-node-certificate-security"></a>Zertifikatsicherheit (Client zu Knoten)

Richten Sie die Client-zu-Knoten-Zertifikatsicherheit beim Erstellen des Clusters im Azure-Portal, über Azure Resource Manager-Vorlagen oder eine eigenständige JSON-Vorlage ein. Um das Zertifikat zu erstellen, geben Sie ein Clientzertifikat für einen Administrator oder einen Benutzer an. Als bewährte Methode gilt: Die angegebenen Administrator- und Benutzerclientzertifikate müssen sich von den primären und sekundären Zertifikaten unterscheiden, die Sie für [Knoten-zu-Knoten-Sicherheit](#node-to-node-security) angeben. Clusterzertifikate verfügen über dieselben Rechte wie Clientadministratorzertifikate. Sie sollten jedoch als bewährte Sicherheitsmethode nur vom Cluster und nicht von Administratoren verwendet werden.

Clients, die unter Verwendung des Administratorzertifikats eine Verbindung mit dem Cluster herstellen, haben uneingeschränkten Zugriff auf die Verwaltungsfunktionen. Clients, die unter Verwendung des schreibgeschützten Benutzerclientzertifikats eine Verbindung mit dem Cluster herstellen, haben nur Lesezugriff auf die Verwaltungsfunktionen. Diese Zertifikate werden für die später in diesem Artikel beschriebene Service Fabric RBAC verwendet.

Informationen zum Einrichten der Zertifikatsicherheit in einem Cluster für Azure finden Sie unter [Einrichten eines Clusters mit einer Azure Resource Manager-Vorlage](service-fabric-cluster-creation-via-arm.md).

Informationen zum Einrichten der Zertifikatsicherheit in einem Cluster für einen eigenständigen Windows Server-Cluster finden Sie unter [Schützen eines eigenständigen Windows-Clusters mithilfe von X.509-Zertifikaten](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Client-zu-Knoten-Sicherheit von Azure Active Directory in Azure

Mit Azure AD können Organisationen (so genannte Mandanten) den Benutzerzugriff auf Anwendungen verwalten. Bei den Anwendungen wird zwischen Anwendungen mit webbasierter Anmeldebenutzeroberfläche und Anwendungen mit nativer Clientumgebung unterschieden. Falls Sie noch keinen Mandanten erstellt haben, sollten Sie zunächst den Artikel [Schnellstart: Einrichten einer Entwicklungsumgebung][active-directory-howto-tenant] lesen.

Service Fabric-Cluster bieten unterschiedliche Einstiegspunkte für ihre Verwaltungsfunktionen. Hierzu zählen etwa [Service Fabric Explorer][service-fabric-visualizing-your-cluster] (webbasiert) und [Visual Studio][service-fabric-manage-application-in-visual-studio]. Daher erstellen Sie zwei Azure AD-Anwendungen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung.

Für in Azure ausgeführte Cluster können Sie den Zugriff auf die Verwaltungsendpunkte auch mit Azure Active Directory (Azure AD) schützen. Um zu erfahren, wie die benötigten Azure AD-Artefakte erstellt und bei der Erstellung des Clusters mit Daten aufgefüllt werden, lesen Sie [Einrichten von Azure AD, um Clients zu authentifizieren](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Sicherheitsempfehlungen

Für Service Fabric-Cluster, die in einem öffentlichen, in Azure gehosteten Netzwerk bereitgestellt werden, wird im Zusammenhang mit der gegenseitigen Client-zu-Knoten-Authentifizierung Folgendes empfohlen:

* Verwendung von Azure Active Directory für die Clientidentität
* Ein Zertifikat für die Serveridentität und TLS-Verschlüsselung der HTTP-Kommunikation

Für Service Fabric-Cluster, die in einem öffentlichen, in Azure gehosteten Netzwerk bereitgestellt werden, wird im Zusammenhang mit der Knoten-zu-Knoten-Sicherheit die Verwendung eines Clusterzertifikats zum Authentifizieren von Knoten empfohlen.

Für eigenständige Windows Server-Cluster sollten Sie die Windows-Sicherheit mit gruppenverwalteten Dienstkonten verwenden, wenn Sie über Windows Server 2012 R2 und Windows Active Directory verfügen. Verwenden Sie andernfalls die Windows-Sicherheit mit Windows-Konten.

## <a name="service-fabric-role-based-access-control"></a>Rollenbasierte Service Fabric-Zugriffssteuerung

Mit der Zugriffssteuerung können Sie den Zugriff auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern einschränken. Dadurch wird der Cluster sicherer. Für Clients, die eine Clusterverbindung herstellen, werden zwei Zugriffssteuerungen unterstützt: Administratorrolle und Benutzerrolle.

Benutzer mit Administratorrolle haben vollständigen Zugriff auf Verwaltungsfunktionen (einschließlich Lese-/Schreibzugriff). Benutzer mit Benutzerrolle haben standardmäßig nur Lesezugriff auf Verwaltungsfunktionen (z.B. Abfragefunktionen). Sie können auch Anwendungen und Dienste auflösen.

Legen Sie die Administrator- und Benutzerclientrollen bei der Erstellung des Clusters fest. Weisen Sie Rollen zu, indem Sie verschiedene Identitäten für jeden Rollentyp bereitstellen (z.B. durch Verwendung von Zertifikaten oder Azure AD). Weitere Informationen zu den Standardeinstellungen der Zugriffssteuerung sowie zum Ändern der Standardeinstellungen finden Sie unter [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509-Zertifikate und Service Fabric

Digitale X.509-Zertifikate werden in der Regel verwendet, um Clients und Server zu authentifizieren. Sie werden auch zum Verschlüsseln und digitalen Signieren von Nachrichten verwendet. Service Fabric verwendet X.509-Zertifikate, um einen Cluster zu sichern und Sicherheitsfunktionen für Anwendungen bereitzustellen. Weitere Informationen zu digitalen X. 509-Zertifikaten finden Sie unter [Verwenden von Zertifikaten](/dotnet/framework/wcf/feature-details/working-with-certificates). [Key Vault](../key-vault/general/overview.md) dient zum Verwalten von Zertifikaten für Service Fabric-Cluster in Azure.

Einige wichtige Punkte sind zu beachten:

* Um Zertifikate für Cluster zu erstellen, die Produktionsworkloads ausführen, verwenden Sie einen korrekt konfigurierten Windows Server-Zertifikatsdienst oder den einer genehmigten [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) (Certificate Authority, CA).
* Verwenden Sie niemals temporäre oder Testzertifikate, die Sie mithilfe von Tools wie MakeCert.exe in einer Produktionsumgebung erstellen.
* Selbstsignierte Zertifikate können Sie nur in einem Testcluster verwenden. Verwenden Sie kein selbstsigniertes Zertifikat in der Produktion.
* Achten Sie beim Generieren des Zertifikatfingerabdrucks darauf, dass Sie einen SHA-1-Fingerabdruck generieren. SHA-1 wird beim Konfigurieren der Client- und Clusterzertifikatfingerabdrücke verwendet.

### <a name="cluster-and-server-certificate-required"></a>Cluster- und Serverzertifikat (erforderlich)

Diese Zertifikate (ein primäres und optional ein sekundäres Zertifikat) sind erforderlich, um einen Cluster zu schützen und nicht autorisierten Zugriff zu verhindern. Diese Zertifikate stellen Cluster- und Serverauthentifizierung bereit.

Clusterauthentifizierung authentifiziert die Kommunikation zwischen Knoten für einen Clusterverbund. Nur Knoten, die ihre Identität mit diesem Zertifikat nachweisen können, dürfen dem Cluster beitreten. Serverauthentifizierung authentifiziert die Verwaltungsendpunkte des Clusters bei einem Verwaltungsclient, sodass der Verwaltungsclient weiß, dass die Kommunikation wirklich mit dem Cluster und nicht mit einem „Man in the Middle“ erfolgt. Dieses Zertifikat stellt auch TLS für die HTTPS-Verwaltungs-API und für Service Fabric Explorer über HTTPS bereit. Bei der Authentifizierung eines Knotens durch einen Client oder Knoten besteht eine der ersten Prüfungen darin, den Wert des allgemeinen Namens im Feld **Antragsteller** zu prüfen. Dieser allgemeine Name oder einer der alternativen Antragstellernamen (Subject Alternative Names, SANs) der Zertifikate muss in der Liste der zulässigen allgemeinen Namen vorhanden sein.

Das Zertifikat muss die folgenden Anforderungen erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten. Diese Zertifikate weisen in der Regel die Erweiterung „.pfx“ oder „.pem“ auf.  
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden, um in eine PFX-Datei (Personal Information Exchange; privater Informationsaustausch) exportiert werden zu können.
* Der **Name des Antragstellers für das Zertifikat muss der Domäne entsprechen, über die Sie auf den Service Fabric-Cluster zugreifen**. Dies ist erforderlich, damit TLS für den HTTPS-Verwaltungsendpunkt des Clusters und für Service Fabric Explorer bereitgestellt werden kann. Für die Domäne *.cloudapp.azure.com können Sie kein TLS/SSL-Zertifikat von einer Zertifizierungsstelle beziehen. Sie benötigen einen benutzerdefinierten Domänennamen für Ihren Cluster. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat dem benutzerdefinierten Domänennamen entsprechen, den Sie für Ihren Cluster verwenden.

Einige weitere Aspekte, die zu berücksichtigen sind:

* Das Feld **Antragsteller** kann mehrere Werte enthalten. Der Typ jedes Werts wird durch ein Initialisierungspräfix angegeben. In der Regel lautet die Initialisierung **CN** (für *Common Name*, den allgemeinen Namen); z. B. **CN = www\.contoso.com**.
* Das Feld **Antragsteller** kann leer sein.
* Wenn das optionale Feld **Alternativer Antragstellername** ausgefüllt ist, muss es sowohl den allgemeinen Namen des Zertifikats als auch einen Eintrag pro SAN enthalten. Diese werden als Werte für **DNS-Name** eingegeben. Informationen zum Generieren von Zertifikaten mit SANs finden Sie unter [How to add a Subject Alternative Name to a secure LDAP certificate](https://support.microsoft.com/kb/931351) (Hinzufügen eines alternativen Antragstellernamens zu einem geschützten LDAP-Zertifikat).
* Der Wert des Felds **Beabsichtigte Zwecke** des Zertifikats muss einen entsprechenden Wert enthalten, z.B. **Serverauthentifizierung** oder **Clientauthentifizierung**.

### <a name="application-certificates-optional"></a>Anwendungszertifikate (optional)

Zum Zweck der Anwendungssicherheit kann eine beliebige Anzahl zusätzlicher Zertifikate in einem Cluster installiert werden. Bevor Sie den Cluster erstellen, betrachten Sie die verschiedenen Szenarien zur Anwendungssicherheit, in denen ein Zertifikat auf den Knoten installiert werden muss, beispielsweise:

* Verschlüsselung und Entschlüsselung von Anwendungskonfigurationswerten
* Knotenübergreifende Verschlüsselung von Daten während der Replikation

Das Konzept der Erstellung sicherer Cluster ist für Linux und Windows identisch.

### <a name="client-authentication-certificates-optional"></a>Clientauthentifizierungszertifikate (optional)

Eine beliebige Anzahl weiterer Zertifikate kann für Administrator- oder Benutzerclientvorgänge angegeben werden. Der Client kann diese Zertifikate verwenden, wenn eine gegenseitige Authentifizierung erforderlich ist. Clientzertifikate werden in der Regel nicht von einer Drittanbieter-Zertifizierungsstelle ausgestellt. Stattdessen enthält der persönliche Speicher des aktuellen Benutzerspeicherorts in der Regel Clientzertifikate, die dort von einer Stammzertifizierungsstelle bereitgestellt werden. Das Zertifikat sollte als Wert für **Beabsichtigte Zwecke** die Angabe **Clientauthentifizierung** aufweisen.  

Standardmäßig hat das Clusterzertifikat Administratorrechte für den Client. Diese zusätzlichen Clientzertifikate sollten nicht im Cluster installiert werden, sondern werden in der Clusterkonfiguration als zulässig angegeben.  Die Clientzertifikate müssen jedoch auf den Clientcomputern installiert werden, um eine Verbindung mit dem Cluster herzustellen und Vorgänge auszuführen.

> [!NOTE]
> Für sämtliche Verwaltungsvorgänge für einen Service Fabric-Cluster sind Serverzertifikate erforderlich. Clientzertifikate können für Verwaltungsaufgaben nicht verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Erstellen eines Clusters mit dem Azure-Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
