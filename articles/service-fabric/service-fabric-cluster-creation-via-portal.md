---
title: Erstellen eines Service Fabric-Clusters im Azure-Portal | Microsoft Docs
description: Erfahren Sie, wie Sie über das Azure-Portal und mithilfe von Azure Key Vault einen sicheren Service Fabric-Cluster in Azure erstellen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 02312a19c687908b0e1c0e6417dc6b0a9df23912
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125084"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Diese schrittweise Anleitung führt Sie durch die Einrichtung eines Service Fabric-Clusters (Linux oder Windows) in Azure über das Azure-Portal. In diesem Leitfaden werden die folgenden Schritte behandelt:

* Erstellen eines Clusters in Azure über das Azure-Portal.
* Authentifizieren von Administratoren mithilfe von Zertifikaten.

> [!NOTE]
> Um erweiterte Sicherheitsoptionen zu nutzen, wie z.B. Benutzerauthentifizierung mit Azure Active Directory und Zertifikate für die Anwendungssicherheit, [erstellen Sie Ihren Cluster mithilfe von Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Clustersicherheit 
Zertifikate werden in Service Fabric zur Authentifizierung und Verschlüsselung verwendet, um verschiedene Aspekte eines Clusters und der zugehörigen Anwendungen zu sichern. Weitere Informationen zur Verwendung von Zertifikaten in Service Fabric finden Sie unter [Szenarien für die Clustersicherheit in Service Fabric][service-fabric-cluster-security].

Wenn Sie zum ersten Mal einen Service Fabric-Cluster erstellen oder einen Cluster für Testworkloads bereitstellen, können Sie mit dem nächsten Abschnitt (**Erstellen eines Clusters im Azure-Portal**) fortfahren und das System Zertifikate generieren lassen, die für Ihre Cluster benötigt werden, die Testworkloads ausführen. Wenn Sie einen Cluster für Produktionsworkloads einrichten, lesen Sie weiter.

#### <a name="cluster-and-server-certificate-required"></a>Cluster- und Serverzertifikat (erforderlich)
Dieses Zertifikat wird benötigt, um einen Cluster zu sichern und nicht autorisierte Zugriffe auf den Cluster zu verhindern. Es sorgt auf unterschiedliche Weise für Clustersicherheit:

* **Clusterauthentifizierung:** Authentifiziert die Kommunikation zwischen Knoten für einen Clusterverbund. Nur Knoten, die ihre Identität mit diesem Zertifikat nachweisen können, dürfen dem Cluster beitreten.
* **Serverauthentifizierung:** Authentifiziert die Verwaltungsendpunkte des Clusters bei einem Verwaltungsclient, sodass der Verwaltungsclient weiß, dass die Kommunikation tatsächlich mit dem Cluster erfolgt. Dieses Zertifikat stellt auch SSL für die HTTPS-Verwaltungs-API und für Service Fabric Explorer über HTTPS bereit.

Für diese Zwecke muss das Zertifikat die folgenden Anforderungen erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
* Der **Name des Antragstellers für das Zertifikat muss der Domäne entsprechen**, über die auf den Service Fabric-Cluster zugegriffen wird. Dies ist erforderlich, damit SSL für die HTTPS-Verwaltungsendpunkte des Clusters und für Service Fabric Explorer bereitgestellt werden kann. Für die Domäne `.cloudapp.azure.com` können Sie kein SSL-Zertifikat von einer Zertifizierungsstelle beziehen. Erwerben Sie einen benutzerdefinierten Domänennamen für Ihren Cluster. Wenn Sie ein Zertifikat von einer Zertifizierungsstelle anfordern, muss der Name des Antragstellers für das Zertifikat dem benutzerdefinierten Domänennamen entsprechen, der für Ihren Cluster verwendet wird.

#### <a name="client-authentication-certificates"></a>Clientauthentifizierungszertifikate
Zusätzliche Clientzertifikate authentifizieren Administratoren für Clusterverwaltungsaufgaben. Service Fabric weist zwei Zugriffsebenen auf: **Administrator** und **schreibgeschützter Benutzer**. Es sollte mindestens ein einzelnes Zertifikat für den Administratorzugriff verwendet werden. Um weiteren Zugriff auf Benutzerebene zu ermöglichen, muss ein separates Zertifikat bereitgestellt werden. Weitere Informationen zu Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients][service-fabric-cluster-security-roles].

Zur Verwendung von Service Fabric müssen keine Clientauthentifizierungszertifikate in Key Vault hochgeladen werden. Diese Zertifikate müssen nur für Benutzer bereitgestellt werden, die zur Clusterverwaltung autorisiert sind. 

> [!NOTE]
> Zur Authentifizierung von Clients für Clusterverwaltungsvorgänge wird Azure Active Directory empfohlen. Um Azure Active Directory verwenden zu können, müssen Sie [den Cluster mithilfe von Azure Resource Manager erstellen][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Anwendungszertifikate (optional)
Zum Zweck der Anwendungssicherheit kann eine beliebige Anzahl zusätzlicher Zertifikate in einem Cluster installiert werden. Bevor Sie den Cluster erstellen, betrachten Sie die verschiedenen Szenarien zur Anwendungssicherheit, in denen ein Zertifikat auf den Knoten installiert werden muss, beispielsweise:

* Verschlüsselung und Entschlüsselung von Anwendungskonfigurationswerten
* Knotenübergreifende Verschlüsselung von Daten während der Replikation 

Anwendungszertifikate können nicht konfiguriert werden, wenn Sie [einen Cluster über das Azure-Portal erstellen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Um Anwendungszertifikate während der Einrichtung eines Clusters zu konfigurieren, müssen Sie [den Cluster mithilfe von Azure Resource Manager erstellen][create-cluster-arm]. Sie können dem Cluster nach der Erstellung auch Anwendungszertifikate hinzufügen.

## <a name="create-cluster-in-the-azure-portal"></a>Erstellen eines Clusters im Azure-Portal

Das Erstellen eines Produktionsclusters für Ihre Anwendungsanforderungen erfordert eine gewisse Planung. Um Sie dabei zu unterstützen, wird dringend empfohlen, das Sie das Dokument [Planungserwägungen für Service Fabric-Cluster][service-fabric-cluster-capacity] lesen und verstehen. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Suchen nach der Service Fabric-Clusterressource

Melden Sie sich beim [Azure-Portal][azure-portal] an.
Klicken Sie auf **Ressource erstellen**, um eine neue Ressourcenvorlage hinzuzufügen. Suchen Sie Ihre Service Fabric Cluster-Vorlage im **Marketplace** unter **Alles**.
Wählen Sie **Service Fabric-Cluster** aus der Liste aus.

![Suche nach der Service Fabric-Clustervorlage im Azure-Portal.][SearchforServiceFabricClusterTemplate]

Navigieren Sie zum Blatt **Service Fabric-Cluster**, und klicken Sie auf **Erstellen**.

Das Blatt **Service Fabric-Cluster erstellen** umfasst die folgenden vier Schritte:

### <a name="1-basics"></a>1. Grundlagen
![Screenshot der Erstellung einer neuen Ressourcengruppe.][CreateRG]

Auf dem Blatt mit den Grundeinstellungen müssen die grundlegenden Details für Ihren Cluster angegeben werden.

1. Geben Sie den Namen Ihres Clusters ein.
2. Geben Sie einen **Benutzernamen** und ein **Kennwort** für den Remotedesktop für die virtuellen Computer ein.
3. Stellen Sie sicher, dass Sie das gewünschte **Abonnement** auswählen, in dem Ihr Cluster bereitgestellt werden soll. Dies ist besonders wichtig, wenn Sie über mehrere Abonnements verfügen.
4. Erstellen Sie eine neue **Ressourcengruppe**. Verwenden Sie für die Ressourcengruppe den gleichen Namen wie für den Cluster, um sie später einfacher wiederzufinden. Das ist besonders dann hilfreich, wenn Sie Änderungen an Ihrer Bereitstellung vornehmen oder Ihren Cluster löschen möchten.
   
   > [!NOTE]
   > Auch wenn Sie eine vorhandene Ressourcengruppe verwenden können, empfiehlt es sich, eine neue Ressourcengruppe zu erstellen. Dies erleichtert das Löschen von Clustern und allen von ihnen verwendeten Ressourcen.
   > 
   > 
5. Wählen Sie den **Standort** aus, an dem Sie den Cluster erstellen möchten. Wenn Sie ein vorhandenes Zertifikat verwenden möchten, das Sie bereits in einen Schlüsseltresor hochgeladen haben, müssen Sie die gleiche Region verwenden, in der sich Ihr Schlüsseltresor befindet. 

### <a name="2-cluster-configuration"></a>2. Clusterkonfiguration
![Erstellen eines Knotentyps][CreateNodeType]

Konfigurieren Sie die Clusterknoten. Knotentypen definieren die Größe, die Anzahl und die Eigenschaften der virtuellen Computer. Der Cluster kann über mehrere Knotentypen verfügen. Der primäre Knotentyp (der erste, den Sie im Portal definieren), muss jedoch über mindestens fünf virtuelle Computer verfügen, da dies der Knotentyp ist, in dem Service Fabric-Systemdienste platziert werden. Konfigurieren Sie keine **Platzierungseigenschaften**. Die Standardplatzierungseigenschaft „NodeTypeName“ wird automatisch hinzugefügt.

> [!NOTE]
> Ein gängiges Szenario für mehrere Knotentypen ist eine Anwendung, die einen Front-End-Dienst und einen Back-End-Dienst enthält. Der Front-End-Dienst soll auf kleineren virtuellen Computern (VM-Größen wie z.B. D2_V2) ausgeführt werden, auf denen Ports für das Internet geöffnet sind. Der Back-End-Dienst sollte auf größeren virtuellen Computern (VM-Größen wie z.B. D3_V2, D6_V2, D15_V2 usw.) platziert werden, auf denen keine Ports für das Internet geöffnet sind.
> 

1. Wählen Sie einen Namen für Ihren Knotentyp aus (ein bis zwölf Zeichen, nur Buchstaben und Zahlen).
2. Die **Mindestgröße** von virtuellen Computern für den primären Knotentyp hängt von der **Dauerhaftigkeitsstufe** ab, die Sie für den Cluster auswählen. Der Standardwert für die Dauerhaftigkeitsstufe ist „Bronze“. Weitere Informationen zur Dauerhaftigkeit finden Sie unter [Auswählen der Dauerhaftigkeitsmerkmale für Service Fabric-Cluster][service-fabric-cluster-durability].
3. Wählen Sie die **Größe des virtuellen Computers** aus. VMs der D-Serie verfügen über SSDs (Solid-State Drives) und werden für zustandsbehaftete Anwendungen sehr empfohlen. Verwenden Sie keine SKU für virtuelle Computer, die über Teilkerne oder über weniger als 10 GB verfügbaren Speicherplatz auf dem Datenträger verfügt. Lesen Sie das [Dokument zu Planungserwägungen für Service Fabric-Cluster][service-fabric-cluster-capacity], um Hilfe bei der Auswahl der Größe des virtuellen Computers zu erhalten.
4.  **Cluster mit einem Knoten und Cluster mit drei Knoten** sind nur für Testzwecke vorgesehen. Sie werden für ausgeführte Produktionsworkloads nicht unterstützt.
5. Wählen Sie die **Anfängliche Kapazität der VM-Skalierungsgruppe** für den Knotentyp aus. Sie können die Anzahl von VMs in einem Knotentyp später zentral hoch- oder herunterskalieren, für den primären Knotentyp beträgt die Mindestanzahl jedoch fünf für Produktionsworkloads. Andere Knotentypen können über mindestens einen virtuellen Computer verfügen. Die **Mindestanzahl** von virtuellen Computern für den primären Knotentyp steuert die **Zuverlässigkeit** des Clusters.  
6. Konfigurieren Sie **benutzerdefinierte Endpunkte**. In diesem Feld können Sie eine durch Trennzeichen getrennte Liste der Ports eingeben, die Sie über den Azure Load Balancer verfügbar machen möchten, damit Ihre Anwendungen auf das öffentliche Internet zugreifen können. Wenn Sie z.B. die Bereitstellung einer Webanwendung in Ihrem Cluster planen, geben Sie hier „80“ ein, um Datenverkehr in Ihren Cluster über Port 80 zuzulassen. Weitere Informationen zu Endpunkten finden Sie unter [Kommunikation mit Anwendungen][service-fabric-connect-and-communicate-with-services].
7. **Aktivieren Sie den Reverseproxy**.  Der [Service Fabric-Reverseproxy](service-fabric-reverseproxy.md) unterstützt die in einem Service Fabric-Cluster ausgeführten Microservices beim Ermitteln von und Kommunizieren mit anderen Diensten mit HTTP-Endpunkten.
8. Konfigurieren Sie zurück auf dem Blatt **Clusterkonfiguration** unter **+ Optionale Einstellungen anzeigen** die **Diagnose** für den Cluster. Die Diagnose ist standardmäßig in Ihrem Cluster aktiviert, um die Behebung von Problemen in Ihrem Cluster zu vereinfachen. Zum Deaktivieren der Diagnose ändern Sie den **Status** in **Aus**. Das Ausschalten der Diagnose wird **nicht** empfohlen. Wenn Sie bereits ein Application Insights-Projekt erstellt haben, geben Sie seinen Schlüssel an, damit die Anwendungsablaufverfolgungen an dieses Projekt weitergeleitet werden.
9. **Schließen Sie den DNS-Dienst ein**.  Der [DNS-Dienst](service-fabric-dnsservice.md) ist ein optionaler Dienst, der es Ihnen ermöglicht, andere Dienste zu finden, die das DNS-Protokoll verwenden.
10. Wählen Sie den gewünschten **Fabric-Upgrademodus** für Ihren Cluster aus. Wählen Sie **Automatisch**aus, wenn für Ihren Cluster automatisch ein Upgrade auf die neueste verfügbare Version durchgeführt werden soll. Legen Sie den Modus auf **Manuell**fest, wenn Sie selbst eine unterstützte Version auswählen möchten. Weitere Informationen zum Fabric-Upgrademodus finden Sie unter [Upgrade von Service Fabric-Clustern][service-fabric-cluster-upgrade].

> [!NOTE]
> Wir unterstützen nur Cluster, die unterstützte Versionen von Service Fabric ausführen. Bei Verwendung des **manuellen** Modus müssen Sie Ihren Cluster eigenverantwortlich auf eine unterstützte Version upgraden.
> 

### <a name="3-security"></a>3. Sicherheit
![Screenshot der Sicherheitskonfigurationen im Azure-Portal.][BasicSecurityConfigs]

Um Ihnen das Einrichten eines sicheren Testclusters zu erleichtern, stellen wir die Option **Basic** bereit. Wenn Sie bereits über ein Zertifikat verfügen und dieses in Ihren [Schlüsseltresor](/azure/key-vault/) hochgeladen (und den Schlüsseltresor für die Bereitstellung aktiviert) haben, verwenden Sie die Option **Benutzerdefiniert**.

#### <a name="basic-option"></a>Optionen „Basic“
Folgen Sie den Anweisungen der Bildschirme zum Hinzufügen oder Wiederverwenden eines vorhandenen Schlüsseltresors sowie zum Hinzufügen eines Zertifikats. Das Hinzufügen des Zertifikats ist ein synchroner Vorgang, sodass Sie auf die Erstellung des Zertifikats warten müssen.

Widerstehen Sie der Versuchung, diesen Bildschirm zu verlassen, bis der vorherige Vorgang abgeschlossen ist.

![CreateKeyVault]

Da der Schlüsseltresor nun erstellt wurde, bearbeiten Sie die Zugriffsrichtlinien für Ihren Schlüsseltresor. 

![CreateKeyVault2]

Klicken Sie auf **Zugriffsrichtlinien bearbeiten** und dann auf **Erweiterte Zugriffsrichtlinien anzeigen**, und aktivieren Sie den Zugriff auf Azure Virtual Machines für die Bereitstellung. Es wird empfohlen, auch die Vorlagenbereitstellung zu aktivieren. Nachdem Sie Ihre Auswahl getroffen haben, vergessen Sie nicht, auf die Schaltfläche **Speichern** zu klicken und den Bereich **Zugriffsrichtlinien** zu schließen.

![CreateKeyVault3]

Geben Sie den Namen des Zertifikats ein, und klicken Sie auf dann auf **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Option „Benutzerdefiniert“
Überspringen Sie diesen Abschnitt, wenn Sie bereits die Schritte für die Option **Basic** ausgeführt haben.

![SecurityCustomOption]

Sie benötigen den Quellschlüsseltresor, die Zertifikat-URL und Informationen zum Zertifikatfingerabdruck, um die Sicherheitsseite zu vervollständigen. Wenn Sie diese Daten nicht zur Hand haben, öffnen Sie ein weiteres Browserfenster, und gehen Sie dann im Azure-Portal folgendermaßen vor:

1. Navigieren Sie zu Ihrem Schlüsseltresordienst.
2. Wählen Sie die Registerkarte „Eigenschaften“ aus, und kopieren Sie „RESSOURCEN-ID“ in „Quellschlüsseltresor“ im anderen Browserfenster. 

    ![CertInfo0]

3. Wählen Sie nun die Registerkarte „Zertifikate“ aus.
4. Klicken Sie auf den Zertifikatfingerabdruck. Sie gelangen nun auf die Seite „Versionen“.
5. Klicken Sie auf die GUIDs, die unter der aktuellen Version angezeigt werden.

    ![CertInfo1]

6. Nun sollte ein Bildschirm wie der unten abgebildete angezeigt werden. Kopieren Sie den SHA-1-Fingerabdruck im Hexadezimalformat in „Zertifikatfingerabdruck“ im anderen Browserfenster.
7. Kopieren Sie die „Geheimnis-ID“ in „Zertifikat-URL“ im anderen Browserfenster.

    ![CertInfo2]

Aktivieren Sie das Kontrollkästchen **Erweiterte Einstellungen konfigurieren**, um Clientzertifikate für den **Verwaltungsclient** und den **schreibgeschützten Client** einzugeben. Geben Sie in diesen Feldern ggf. den Fingerabdruck des Verwaltungsclientzertifikats und den Fingerabdruck des schreibgeschützten Benutzerclientzertifikats ein. Wenn Administratoren versuchen, eine Verbindung mit dem Cluster herzustellen, wird ihnen nur dann Zugriff gewährt, wenn sie über ein Zertifikat mit einem Fingerabdruck verfügen, der mit den hier angegebenen Fingerabdruckwerten übereinstimmt.  

### <a name="4-summary"></a>4. Zusammenfassung

Nun sind Sie bereit, den Cluster bereitzustellen. Bevor Sie diese Aufgabe ausführen, laden Sie das Zertifikat herunter. Den Link finden Sie im großen blauen Infofeld. Stellen Sie sicher, dass Sie das Zertifikat an einem sicheren Ort aufbewahren. Sie benötigen es, um eine Verbindung mit Ihrem Cluster herzustellen. Da das Zertifikat, das Sie heruntergeladen haben, kein Kennwort besitzt, wird empfohlen, ein Kennwort hinzuzufügen.

Um die Clustererstellung abzuschließen, klicken Sie auf **Erstellen**. Sie können optional die Vorlage herunterladen.

![Zusammenfassung]

Sie können den Verlauf der Erstellung in den Benachrichtigungen finden. (Klicken Sie auf das Glockensymbol in der Nähe der Statusleiste am oberen rechten Bildschirmrand.) Wenn Sie beim Erstellen des Clusters auf **An Startmenü anheften** geklickt haben, wird im Menü **Start** der Hinweis **Deploying Service Fabric Cluster** (Service Fabric-Cluster wird bereitgestellt) angezeigt. Dieser Vorgang nimmt einige Zeit in Anspruch. 

Um Verwaltungsvorgänge auf Ihrem Cluster mit PowerShell oder der CLI auszuführen, müssen Sie eine Verbindung mit Ihrem Cluster herstellen. Lesen Sie mehr dazu unter [Herstellen einer Verbindung mit Ihrem Cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Anzeigen des Clusterstatus
![Screenshot der Clusterdetails im Dashboard.][ClusterDashboard]

Sobald der Cluster erstellt wurde, können Sie Ihren Cluster im Portal überprüfen.

1. Wechseln Sie zu **Durchsuchen**, und klicken Sie auf **Service Fabric-Cluster**.
2. Suchen Sie Ihren Cluster, und klicken Sie darauf.
3. Jetzt sehen Sie die Details Ihres Clusters im Dashboard, einschließlich des öffentlichen Endpunkts des Clusters und eines Links zu Service Fabric Explorer.

Der Abschnitt **Knotenmonitor** auf dem Dashboardblatt des Clusters gibt die Anzahl von virtuellen Computern an, die fehlerfrei bzw. fehlerhaft sind. Weitere Informationen zur Clusterintegrität finden Sie unter [Einführung in das Service Fabric-Integritätsmodell][service-fabric-health-introduction].

> [!NOTE]
> Um die Verfügbarkeit zu gewährleisten und den Zustand beizubehalten, muss eine bestimmte Anzahl von Knoten in einem Service Fabric-Cluster stets in Betrieb sein. Dies wird auch als „Aufrechterhalten eines Quorums“ bezeichnet. Aus Sicherheitsgründen empfiehlt es sich in der Regel nicht, alle Computer innerhalb des Clusters herunterzufahren, sofern Sie zuvor keine [vollständige Sicherung des Zustands][service-fabric-reliable-services-backup-restore] durchgeführt haben.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Herstellen einer Remoteverbindung mit einer Instanz der VM-Skalierungsgruppe oder einem Clusterknoten
Für jeden Knotentyp, den Sie in Ihrem Cluster angeben, wird jeweils eine VM-Skalierungsgruppe eingerichtet. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Nächste Schritte
Sie verfügen jetzt über einen sicheren Cluster, der Zertifikate zur Verwaltungsauthentifizierung verwendet. Als Nächstes [stellen Sie eine Verbindung mit dem Cluster her](service-fabric-connect-to-secure-cluster.md) und erfahren, wie Sie [Anwendungsgeheimnisse verwalten](service-fabric-application-secret-management.md).  Informieren Sie sich auch über [Service Fabric-Supportoptionen](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Zusammenfassung]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
