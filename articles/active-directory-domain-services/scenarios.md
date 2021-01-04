---
title: Gängige Bereitstellungsszenarien für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie mehr über einige der gängigen Szenarien und Anwendungsfälle für Azure Active Directory Domain Services, um für einen Mehrwert zu sorgen und Geschäftsanforderungen zu erfüllen.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619027"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Gängige Anwendungsfälle und Szenarien für Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste wie Domänenbeitritt, Gruppenrichtlinien, Lightweight Directory Access Protocol (LDAP) und Kerberos/NTLM-Authentifizierung bereit. Azure AD DS lässt sich in Ihren vorhandenen Azure AD-Mandanten integrieren, sodass Benutzer sich mit ihren vorhandenen Anmeldeinformationen anmelden können. Sie verwenden diese Domänendienste, ohne Domänencontroller in der Cloud bereitstellen, verwalten und patchen zu müssen, was eine reibungslosere Migration lokaler Ressourcen per Lift & Shift zu Azure ermöglicht.

In diesem Artikel werden einige gängige Geschäftsszenarien beschrieben, in denen Azure AD DS einen Mehrwert bietet und die entsprechenden Anforderungen erfüllt.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Allgemeine Methoden zum Bereitstellen von Identitätslösungen in der Cloud

Wenn Sie vorhandene Workloads zur Cloud migrieren, können verzeichnisbasierte Anwendungen LDAP verwenden, um Lese- oder Schreibzugriff auf ein lokales AD DS-Verzeichnis zu erhalten. Unter Windows Server ausgeführte Anwendungen werden normalerweise auf virtuellen Computern bereitgestellt, die in eine Domäne eingebunden sind. So können sie per Gruppenrichtlinie auf sichere Weise verwaltet werden. Zum Authentifizieren von Endbenutzern nutzen die Anwendungen möglicherweise auch eine integrierte Windows-Authentifizierung wie Kerberos oder NTLM.

IT-Administratoren verwenden häufig eine der folgenden Lösungen, um in Azure ausgeführten Anwendungen einen Identitätsdienst bereitzustellen:

* Konfigurieren einer Site-to-Site-VPN-Verbindung zwischen in Azure ausgeführten Workloads und einer lokalen AD DS-Umgebung
    * Die lokalen Domänencontroller stellen dann die Authentifizierung über die VPN-Verbindung bereit.
* Erstellen von Replikatdomänencontrollern mithilfe von virtuellen Azure-Computern, um die AD DS-Domäne bzw. -Gesamtstruktur von der lokalen Umgebung aus zu erweitern
    * Die auf virtuellen Azure-Computern ausgeführten Domänencontroller stellen die Authentifizierung bereit und replizieren Verzeichnisinformationen innerhalb der lokalen AD DS-Umgebung.
* Bereitstellen einer eigenständigen AD DS-Umgebung in Azure mithilfe von Domänencontrollern, die auf Azure-VMs ausgeführt werden
    * Die auf virtuellen Azure-Computern ausgeführten Domänencontroller stellen die Authentifizierung bereit, es werden jedoch keine Verzeichnisinformationen aus einer lokalen AD DS-Umgebung repliziert.

Bei diesen Ansätzen sind Anwendungen aufgrund der VPN-Verbindungen zum lokalen Verzeichnis anfällig für vorübergehende Netzwerkprobleme oder -ausfälle. Wenn Sie Domänencontroller auf virtuellen Computern in Azure bereitstellen, muss das IT-Team diese virtuellen Computer verwalten, schützen, patchen, überwachen und sichern sowie mögliche Probleme beheben.

Mit Azure AD DS entfällt die Notwendigkeit, VPN-Verbindungen zurück zu einer lokalen AD DS-Umgebung zu erstellen oder VMs in Azure auszuführen und zu verwalten, um Identitätsdienste bereitzustellen. Azure AD DS ist ein verwalteter Dienst und vereinfacht als solcher die Erstellung einer integrierten Identitätslösung für hybride und rein cloudbasierte Umgebungen erheblich.

> [!div class="nextstepaction"]
> [Vergleichen von Azure AD DS mit Azure AD und selbstverwalteten AD DS auf Azure-VMs oder lokal][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS für Hybridorganisationen

Viele Organisationen betreiben eine hybride Infrastruktur, die sowohl cloudbasierte als auch lokale Anwendungsworkloads umfasst. Ältere Anwendungen, die im Rahmen einer Lift & Shift-Strategie zu Azure migriert wurden, verwenden möglicherweise herkömmliche LDAP-Verbindungen, um Identitätsinformationen bereitzustellen. Zur Unterstützung einer solchen Hybridinfrastruktur können Identitätsinformationen aus einer lokalen AD DS-Umgebung mit einem Azure AD-Mandanten synchronisiert werden. Azure AD DS stellt für diese älteren Anwendungen in Azure dann eine Identitätsquelle bereit, ohne dass die Anwendungskonnektivität wieder in lokalen Verzeichnisdiensten konfiguriert und verwaltet werden muss.

Sehen Sie sich dieses Beispiel der Litware Corporation an, eine hybride Organisation, die sowohl lokale als auch Azure-basierte Ressourcen ausführt:

![Azure Active Directory Domain Services für eine hybride Organisation mit Synchronisierung lokaler Ressourcen](./media/overview/synced-tenant.png)

* Die Anwendungen und Serverworkloads, die Domänendienste erfordern, sind in einem virtuellen Netzwerk in Azure bereitgestellt.
    * Hierzu gehören möglicherweise auch ältere Anwendungen, die im Rahmen einer Lift & Shift-Strategie zu Azure migriert wurden.
* Um Identitätsinformationen aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten zu synchronisieren, stellt die Litware Corporation [Azure AD Connect][azure-ad-connect] bereit.
    * Die synchronisierten Identitätsinformationen umfassen Benutzerkonten und Gruppenmitgliedschaften.
* Das IT-Team von Litware aktiviert Azure AD DS für den Azure AD-Mandanten in diesem Netzwerk oder in einem virtuellen Peeringnetzwerk.
* Anwendungen und VMs, die auf virtuellen Computern im virtuellen Azure-Netzwerk bereitgestellt werden, können dann Azure AD DS-Features wie Domänenbeitritt, LDAP-Lesevorgänge, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung sowie Gruppenrichtlinien nutzen.

> [!IMPORTANT]
> Azure AD Connect sollte nur für die Synchronisierung mit lokalen AD DS-Umgebungen installiert und konfiguriert werden. Die Installation von Azure AD Connect in einer verwalteten Domäne zur erneuten Synchronisierung von Objekten mit Azure AD wird nicht unterstützt.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS für rein cloudbasierte Organisationen

Ein rein cloudbasierter Azure AD-Mandant besitzt keine lokale Identitätsquelle. Benutzerkonten und Gruppenmitgliedschaften werden beispielsweise direkt in Azure AD erstellt und verwaltet.

Sehen wir uns jetzt ein Beispiel für Contoso an, eine reine Cloudorganisation, die zur Identitätsverwaltung Azure AD verwendet. Alle Benutzeridentitäten sowie die zugehörigen Anmeldeinformationen und Gruppenmitgliedschaften werden in Azure AD erstellt und verwaltet. Es ist keine zusätzliche Konfiguration von Azure AD Connect erforderlich, um Identitätsinformationen aus einem lokalen Verzeichnis zu synchronisieren.

![Azure Active Directory Domain Services für eine reine Cloudorganisation ohne Synchronisierung lokaler Ressourcen](./media/overview/cloud-only-tenant.png)

* Die Anwendungen und Serverworkloads, die Domänendienste erfordern, sind in einem virtuellen Netzwerk in Azure bereitgestellt.
* Das IT-Team von Contoso aktiviert Azure AD DS für den Azure AD-Mandanten in diesem Netzwerk oder in einem virtuellen Peeringnetzwerk.
* Anwendungen und VMs, die auf virtuellen Computern im virtuellen Azure-Netzwerk bereitgestellt werden, können dann Azure AD DS-Features wie Domänenbeitritt, LDAP-Lesevorgänge, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung sowie Gruppenrichtlinien nutzen.

## <a name="secure-administration-of-azure-virtual-machines"></a>Sichere Verwaltung von virtuellen Azure-Computern

Damit Sie die Möglichkeit haben, einen einzelnen Satz AD-Anmeldeinformationen zu verwenden, können virtuelle Azure-Computer (Virtual Machines, VMs) einer verwalteten Azure AD DS-Domäne hinzugefügt werden. Dieser Ansatz minimiert Probleme bei der Verwaltung von Anmeldeinformationen wie die Verwaltung lokaler Administratorkonten auf den einzelnen VMs oder separater Konten und Kennwörter zwischen Umgebungen.

Einer verwalteten Domäne hinzugefügte VMs können auch mithilfe von Gruppenrichtlinien verwaltet und geschützt werden. Erforderliche Sicherheitsbaselines können auf VMs angewendet werden, um die VMs gemäß den Sicherheitsrichtlinien des Unternehmens zu sperren. Beispiel: Sie können mithilfe von Funktionen für die Gruppenrichtlinienverwaltung die Anwendungstypen einschränken, die auf dem virtuellen Computer gestartet werden können.

![Optimierte Verwaltung von virtuellen Azure-Computern](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Im Folgenden wird ein gängiges Beispielszenario beschrieben. Wenn Server und andere Infrastrukturkomponenten das Ende ihrer Lebensdauer erreichen, möchte Contoso die aktuell lokal gehosteten Anwendungen in die Cloud verschieben. Der aktuelle IT-Standard des Unternehmens gibt vor, dass Server, auf denen Unternehmensanwendungen gehostet werden, einer Domäne angehören und mithilfe von Gruppenrichtlinien verwaltet werden müssen.

Der IT-Administrator von Contoso zieht es vor, für in Azure bereitgestellte VMs den Beitritt zu einer Domäne durchzuführen, um die Verwaltung zu vereinfachen, da sich Benutzer mit ihren Unternehmensanmeldeinformationen anmelden können. Wenn die VMs in die Domäne eingebunden sind, können sie mithilfe von Gruppenrichtlinienobjekten (Group Policy Objects, GPOs) auch so konfiguriert werden, dass sie die erforderlichen Sicherheitsbaselines erfüllen. Contoso würde es vorziehen, die eigenen Domänencontroller in Azure nicht bereitstellen, überwachen und verwalten zu müssen.

Für diesen Anwendungsfall ist Azure AD DS hervorragend geeignet. Mit einer verwalteten Domäne können Sie VMs in eine Domäne einbinden, einen einzelnen Satz Anmeldeinformationen verwenden und Gruppenrichtlinien anwenden. Da es sich um eine verwaltete Domäne handelt, müssen Sie die Domänencontroller nicht selbst konfigurieren und verwalten.

### <a name="deployment-notes"></a>Hinweise zur Bereitstellung

Beachten Sie die folgenden Bereitstellungsaspekte für diesen Beispielanwendungsfall:

* Verwaltete Domänen verwenden standardmäßig eine einzige flache Organisationseinheitsstruktur (Organizational Unit, OU). Alle in die Domäne eingebundenen VMs befinden sich in einer einzigen Organisationseinheit. Sie können aber auch [benutzerdefinierte Organisationseinheiten][custom-ou] erstellen.
* Azure AD DS verwendet jeweils ein integriertes Gruppenrichtlinienobjekt (Group Policy Object, GPO) für Benutzer- und Computercontainer. Um weitere Steuerungsmöglichkeiten zu haben, können Sie [benutzerdefinierte Gruppenrichtlinienobjekte erstellen][create-gpo] und diese benutzerdefinierten Organisationseinheiten zuweisen.
* Azure AD DS unterstützt das grundlegende AD-Computerobjektschema. Das Schema des Computerobjekts kann nicht erweitert werden.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Migrieren lokaler Anwendungen per Lift & Shift, die eine LDAP-Bindungsauthentifizierung verwenden

In einem Beispielszenario verfügt Contoso über eine lokale Anwendung, die vor vielen Jahren von einem unabhängigen Softwarehersteller (Independent Software Vendor, ISV) gekauft wurde. Die Anwendung wurde vom ISV in den Wartungsmodus versetzt, und das Anfordern von Änderungen an der Anwendung ist äußerst kostspielig. Diese Anwendung verfügt über ein webbasiertes Front-End, mit dem Benutzeranmeldeinformationen mithilfe eines Webformulars gesammelt werden, und anschließend werden Benutzer authentifiziert, indem eine LDAP-Bindung an die lokale AD DS-Umgebung ausgeführt wird.

![LDAP-Bindung](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso möchte diese Anwendung zu Azure migrieren. Die Anwendung sollte unverändert weiterhin funktionieren, ohne dass Änderungen erforderlich sind. Außerdem sollen sich Benutzer mit ihren vorhandenen Unternehmensanmeldeinformationen und ohne zusätzlichen Schulungsaufwand authentifizieren können. Es sollte für Endbenutzer transparent sein, wo die Anwendung ausgeführt wird.

In diesem Szenario ermöglicht Azure AD DS Anwendungen das Ausführen von LDAP-Bindungen als Teil des Authentifizierungsvorgangs. Ältere lokale Anwendungen können per Lift & Shift zu Azure migriert werden und Benutzer weiterhin nahtlos authentifizieren, ohne dass Änderungen an der Konfiguration oder der Benutzeroberfläche vorgenommen werden müssen.

### <a name="deployment-notes"></a>Hinweise zur Bereitstellung

Beachten Sie die folgenden Bereitstellungsaspekte für diesen Beispielanwendungsfall:

* Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf eine verwaltete Domäne wird nicht unterstützt.
* Sie können Kennwörter nicht direkt bei einer verwalteten Domäne ändern. Endbenutzer können ihr Kennwort entweder mithilfe des [Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD][sspr] oder beim lokalen Verzeichnis ändern. Diese Änderungen werden dann automatisch mit der verwalteten Domäne synchronisiert, sodass sie dort verfügbar sind.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Migrieren lokaler Anwendungen per Lift & Shift, die LDAP-Lesevorgänge zum Zugreifen auf das Verzeichnis verwenden

Wie im vorherigen Beispielszenario gehen wir davon aus, dass Contoso über eine lokale Branchenanwendung verfügt, die vor fast zehn Jahren entwickelt wurde. Diese Anwendung ist verzeichnisfähig und wurde für die Verwendung von LDAP zum Lesen von Informationen/Attributen der Benutzer aus AD DS konzipiert. Die Anwendung ändert keine Attribute und schreibt auch nicht in das Verzeichnis.

Contoso möchte diese Anwendung zu Azure migrieren und die ältere lokale Hardware außer Betrieb nehmen, auf der diese Anwendung aktuell gehostet wird. Die Anwendung kann nicht zur Verwendung moderner Verzeichnis-APIs (wie der REST-basierten Microsoft Graph-API) umgeschrieben werden. Eine Option zum Migrieren per Lift & Shift ist erwünscht, bei der die Anwendung zur Ausführung in der Cloud migriert werden kann, ohne dass Code geändert oder die Anwendung umgeschrieben wird.

Zur Unterstützung dieses Szenarios ermöglicht Azure AD DS Anwendungen die Ausführung von LDAP-Lesevorgängen für die verwaltete Domäne, um die erforderlichen Attributinformationen zu erhalten. Die Anwendung muss nicht umgeschrieben werden, sodass Benutzer durch eine Migration per Lift & Shift zu Azure die App weiterhin verwenden können, ohne zu merken, dass sich der Ort der Ausführung ändert.

### <a name="deployment-notes"></a>Hinweise zur Bereitstellung

Beachten Sie die folgenden Bereitstellungsaspekte für diesen Beispielanwendungsfall:

* Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf eine verwaltete Domäne wird nicht unterstützt.
* Stellen Sie sicher, dass die Anwendung kein benutzerdefiniertes/erweitertes Active Directory-Schema benötigt. Schemaerweiterungen werden in Azure AD DS nicht unterstützt.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrieren eines lokalen Diensts oder einer Daemonanwendung zu Azure

Einige Anwendungen umfassen mehrere Ebenen, wobei eine der Ebenen authentifizierte Aufrufe einer Back-End-Ebene (z. B. einer Datenbank) ausführen muss. In diesen Szenarien werden häufig AD-Dienstkonten verwendet. Wenn Sie Anwendungen per Lift & Shift zu Azure migrieren, können Sie mit Azure AD DS Dienstkonten weiterhin auf die gleiche Weise verwenden. Sie können das gleiche Dienstkonto verwenden, das aus Ihrem lokalen Verzeichnis mit Azure AD synchronisiert wird, oder eine benutzerdefinierte Organisationseinheit erstellen und dann ein separates Dienstkonto in dieser Organisationseinheit erstellen. Bei beiden Ansätzen funktionieren Anwendungen weiterhin auf die gleiche Weise, um authentifizierte Aufrufe anderer Ebenen und Dienste auszuführen.

![Dienstkonto mit WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In diesem Beispielszenario verfügt Contoso über eine benutzerdefinierte Softwaretresoranwendung, die ein Web-Front-End, eine SQL Server-Instanz und einen Back-End-FTP-Server umfasst. Die integrierte Windows-Authentifizierung mithilfe von Dienstkonten authentifiziert das Web-Front-End beim FTP-Server. Das Web-Front-End wird zur Ausführung als Dienstkonto eingerichtet. Der Back-End-Server wird zum Autorisieren des Zugriffs vom Dienstkonto für das Web-Front-End konfiguriert. Contoso möchte nicht die eigenen Domänencontroller-VMs in der Cloud bereitstellen und verwalten, um diese Anwendung in Azure zu verschieben.

In diesem Szenario können die Server, auf denen das Web-Front-End, die SQL Server-Instanz und der FTP-Server gehostet werden, zu Azure-VMs migriert und in eine verwaltete Domäne eingebunden werden. Die VMs können dann das gleiche Dienstkonto im lokalen Verzeichnis für die Authentifizierungszwecke der App verwenden, das über Azure AD mithilfe von Azure AD Connect synchronisiert wird.

### <a name="deployment-notes"></a>Hinweise zur Bereitstellung

Beachten Sie die folgenden Bereitstellungsaspekte für diesen Beispielanwendungsfall:

* Stellen Sie sicher, dass die Anwendungen für die Authentifizierung einen Benutzernamen und ein Kennwort verwenden. Die zertifikat- oder smartcardbasierte Authentifizierung wird von Azure AD DS nicht unterstützt.
* Sie können Kennwörter nicht direkt bei einer verwalteten Domäne ändern. Endbenutzer können ihr Kennwort entweder mithilfe des [Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD][sspr] oder beim lokalen Verzeichnis ändern. Diese Änderungen werden dann automatisch mit der verwalteten Domäne synchronisiert, sodass sie dort verfügbar sind.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Bereitstellungen von Windows Server-Remotedesktopdiensten in Azure

Mit Azure AD DS können Sie verwaltete Domänendienste für in Azure bereitgestellte Remotedesktopserver bereitstellen.

Weitere Informationen zu diesem Bereitstellungsszenario finden Sie unter [Integrieren von Azure AD Domain Services in Ihre RDS-Bereitstellung][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>In die Domäne eingebundene HDInsight-Cluster

Sie können einen in eine verwaltete Domäne eingebundenen Azure HDInsight-Cluster einrichten, für den Apache Ranger aktiviert ist. Sie können Hive-Richtlinien über Apache Ranger erstellen und anwenden und zulassen, dass Benutzer (z. B. Datenanalysten) mithilfe von ODBC-basierten Tools wie Excel oder Tableau eine Verbindung mit Hive herstellen. Wir arbeiten weiterhin daran, andere Workloads wie HBase, Spark und Storm zu HDInsight-Instanzen hinzuzufügen, die in eine Domäne eingebunden sind.

Weitere Informationen zu diesem Bereitstellungsszenario finden Sie unter [Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern][hdinsight].

## <a name="next-steps"></a>Nächste Schritte

Zunächst [erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds