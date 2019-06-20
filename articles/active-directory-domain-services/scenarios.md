---
title: 'Azure Active Directory Domain Services: Bereitstellungsszenarien | Microsoft-Dokumentation'
description: Bereitstellungsszenarien für die Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: ergreenl
ms.openlocfilehash: 0659586512b36c51c5058271fa5e1bdb46efbc3b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245421"
---
# <a name="deployment-scenarios-and-use-cases"></a>Bereitstellungsszenarien und Anwendungsfälle
In diesem Abschnitt betrachten wir einige Szenarien und Anwendungsfälle, die von der Verwendung der Azure Active Directory (AD) Domain Services profitieren.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Sichere und einfache Verwaltung von virtuellen Azure-Computern
Die Azure Active Directory Domain Services ermöglichen eine optimierte Verwaltung Ihrer virtuellen Azure-Computer. Virtuelle Azure-Computer können der verwalteten Domäne hinzugefügt werden, was Ihnen die Verwendung Ihrer Unternehmens-AD-Anmeldeinformationen für die Anmeldung ermöglicht. Mit diesem Ansatz kann der Aufwand bei der Verwaltung von Anmeldeinformationen verhindert werden, beispielsweise die Verwaltung lokaler Administratorkonten auf allen virtuellen Azure-Computern.

Der verwalteten Domäne hinzugefügte virtuelle Computer können auch mithilfe von Gruppenrichtlinien verwaltet und geschützt werden. Sie können gemäß den Sicherheitsrichtlinien Ihres Unternehmens erforderliche Sicherheitsbaselines auf Ihre virtuellen Azure-Computer anwenden und diese sperren. Beispiel: Sie können mithilfe von Funktionen für die Gruppenrichtlinienverwaltung die Anwendungstypen einschränken, die auf diesen virtuellen Computern gestartet werden können.

![Optimierte Verwaltung von virtuellen Azure-Computern](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Wenn Server und andere Infrastruktur das Ende ihrer Lebensdauer erreichen, verlagert Contoso viele Anwendungen in die Cloud, die derzeit noch lokal gehostet werden. Der aktuelle IT-Standard des Unternehmens gibt vor, dass Server, auf denen Unternehmensanwendungen gehostet werden, einer Domäne angehören und per Gruppenrichtlinie verwaltet werden müssen. Der IT-Administrator von Contoso zieht es vor, für unter Azure bereitgestellte virtuelle Computer den Beitritt zu einer Domäne durchzuführen, um die Verwaltung zu vereinfachen. Infolgedessen können sich Administratoren und Benutzer mit ihren Unternehmensanmeldeinformationen anmelden. Gleichzeitig können Computer mithilfe von Gruppenrichtlinien so konfiguriert werden, dass sie die erforderlichen Sicherheitsbaselines erfüllen. Contoso würde es vorziehen, Domänencontroller in Azure nicht bereitstellen, überwachen und verwalten zu müssen, um virtuelle Azure-Computer zu schützen. Daher eignen sich die die Azure AD Domain Services hervorragend für diesen Anwendungsfall.

**Hinweise zur Bereitstellung**

Beachten Sie die folgenden wichtigen Punkte für dieses Bereitstellungsszenario:

* Verwaltete Domänen, die über die Azure AD Domain Services bereitgestellt werden, stellen standardmäßig eine Struktur mit einer einzelnen flachen Organisationseinheit bereit. Alle in die Domäne eingebundenen Computer befinden sich in einer einzelnen flachen Organisationseinheit. Sie können jedoch auch benutzerdefinierte Organisationseinheiten erstellen.
* Azure AD Domain Services unterstützen jeweils eine einfache Gruppenrichtlinie in Form eines integrierten Gruppenrichtlinienobjekts (GPO) für Benutzer- und Computercontainer. Sie können benutzerdefinierte Gruppenrichtlinienobjekte erstellen und auf benutzerdefinierte Organisationseinheiten abstimmen.
* Azure AD Domain Services unterstützen das grundlegende AD-Computerobjektschema. Sie können das Schema des Computerobjekts nicht erweitern.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Verlagern einer lokalen Anwendung, die die LDAP-Bindungsauthentifizierung verwendet, auf Azure-Infrastrukturdienste
![LDAP-Bindung](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso verfügt über eine lokale Anwendung, die vor vielen Jahren von einem Internetdienstanbieter gekauft wurde. Die Anwendung wurde vom Internetdienstanbieter in den Wartungsmodus versetzt, und das Anfordern von Änderungen an der Anwendung ist für Contoso gerade übermäßig teuer. Diese Anwendung verfügt über ein webbasiertes Front-End, mit dem Benutzeranmeldeinformationen per Web Form gesammelt werden, und anschließend werden Benutzer authentifiziert, indem eine LDAP-Bindung an das Active Directory des Unternehmens eingerichtet wird. Contoso möchte diese Anwendung zu den Azure-Infrastrukturdiensten migrieren. Hierbei ist es wünschenswert, dass die Anwendung funktioniert, ohne dass Änderungen erforderlich sind. Außerdem sollen sich Benutzer mit ihren vorhandenen Unternehmensanmeldeinformationen authentifizieren können, und es soll kein Schulungsaufwand zur Vermittlung einer anderen Vorgehensweise anfallen. Anders ausgedrückt: Für Endbenutzer soll nicht erkennbar bzw. relevant sein, wo die Anwendung ausgeführt wird, und die Migration sollte transparent sein.

**Hinweise zur Bereitstellung**

Beachten Sie die folgenden wichtigen Punkte für dieses Bereitstellungsszenario:

* Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die über Azure AD Domain Services bereitgestellt werden, wird nicht unterstützt.
* Sie können Kennwörter nicht direkt basierend auf der verwalteten Domäne ändern. Endbenutzer können ihr Kennwort entweder mithilfe des Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD oder basierend auf dem lokalen Verzeichnis ändern. Diese Änderungen werden automatisch mit der verwalteten Domäne synchronisiert und sind darin verfügbar.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Verlagern einer lokalen Anwendung, die den LDAP-Lesevorgang zum Zugreifen auf das Verzeichnis verwendet, auf Azure-Infrastrukturdienste
Contoso besitzt eine lokale Branchenanwendung, die vor fast zehn Jahren entwickelt wurde. Diese Anwendung ist verzeichnisorientiert und wurde für die Zusammenarbeit mit Windows Server AD entworfen. Die Anwendung nutzt LDAP (Lightweight Directory Access Protocol) zum Lesen von Informationen/Attributen der Benutzer über Active Directory. Von der Anwendung werden keine Attribute geändert, und es werden auch keine anderen Schreibvorgänge in das Verzeichnis durchgeführt. Contoso möchte diese Anwendung zu Azure-Infrastrukturdiensten migrieren und die ältere lokale Hardware aussondern, auf der diese Anwendung derzeit gehostet wird. Die Anwendung kann nicht für die Verwendung moderner Verzeichnis-APIs, z. B. die REST-basierte Azure AD Graph-API, umgeschrieben werden. Aus diesem Grund ist eine Verlagerungsoption („Lift-and-Shift“) hilfreich, bei der die Anwendung für die Ausführung in der Cloud migriert werden kann, ohne dass Code geändert oder die Anwendung umgeschrieben wird.

**Hinweise zur Bereitstellung**

Beachten Sie die folgenden wichtigen Punkte für dieses Bereitstellungsszenario:

* Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die über Azure AD Domain Services bereitgestellt werden, wird nicht unterstützt.
* Stellen Sie sicher, dass die Anwendung kein benutzerdefiniertes/erweitertes Active Directory-Schema benötigt. Schemaerweiterungen werden in Azure AD Domain Services nicht unterstützt.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrieren eines lokalen Diensts oder einer Daemonanwendung zu Azure-Infrastrukturdiensten
Einige Programme bestehen aus mehreren Ebenen, in denen eine Ebene authentifizierte Aufrufe an eine Back-End-Ebene (etwa eine Datenbankebene) ausführen muss. Active Directory-Dienstkonten werden häufig für diese Anwendungsfälle verwendet. Sie können solche Anwendungen per „Lift-and-Shift“ in Azure-Infrastrukturdienste verlagern und Azure AD Domain Services für die Identitätsanforderungen dieser Anwendungen verwenden. Sie können dasselbe Dienstkonto verwenden, das aus Ihrem lokalen Verzeichnis in Azure AD synchronisiert wird. Alternativ können Sie zum Bereitstellen solcher Anwendungen zuerst eine benutzerdefinierte Organisationseinheit und dann ein separates Dienstkonto in dieser Organisationseinheit erstellen.

![Dienstkonto mit WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso verfügt über eine benutzerdefinierte Softwaretresoranwendung mit einem Web-Front-End, einer SQL Server-Instanz und einem Back-End-FTP-Server. Die integrierte Windows-Authentifizierung per Dienstkonto wird genutzt, um das Web-Front-End gegenüber dem FTP-Server zu authentifizieren. Das Web-Front-End wird zur Ausführung als Dienstkonto eingerichtet. Der Back-End-Server wird zum Autorisieren des Zugriffs vom Dienstkonto für das Web-Front-End konfiguriert. Contoso bevorzugt es, keinen virtuellen Computer mit Domänencontroller in der Cloud bereitstellen zu müssen, um diese Anwendung in Azure-Infrastrukturdienste zu verschieben. Der IT-Administrator von Contoso kann die Server, auf denen das Web-Front-End, SQL Server und der FTP-Server gehostet werden, auf virtuellen Azure-Computern bereitstellen. Diese Computer werden dann in eine verwaltete Domäne der Azure AD Domain Services eingebunden. Anschließend kann dasselbe Dienstkonto im lokalen Verzeichnis für die Authentifizierungszwecke der App verwendet werden. Dieses Dienstkonto wird mit der verwalteten Domäne der Azure AD Domain Services synchronisiert und kann verwendet werden.

**Hinweise zur Bereitstellung**

Beachten Sie die folgenden wichtigen Punkte für dieses Bereitstellungsszenario:

* Stellen Sie sicher, dass die Anwendung für die Authentifizierung Benutzername und Kennwort verwendet. Die Authentifizierung per Zertifikat oder Smartcard wird von den Azure AD Domain Services nicht unterstützt.
* Sie können Kennwörter nicht direkt basierend auf der verwalteten Domäne ändern. Endbenutzer können ihr Kennwort entweder mithilfe des Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD oder basierend auf dem lokalen Verzeichnis ändern. Diese Änderungen werden automatisch mit der verwalteten Domäne synchronisiert und sind darin verfügbar.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Bereitstellungen von Windows Server-Remotedesktopdiensten in Azure
Mit Azure AD Domain Services können Sie für Ihre in Azure bereitgestellten Remotedesktopserver verwaltete AD-Domänendienste bereitstellen.

Weitere Informationen zu diesem Bereitstellungsszenario finden Sie unter [Integrieren von Azure AD Domain Services in Ihre RDS-Bereitstellung](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>In die Domäne eingebundene HDInsight-Cluster (Vorschau)
Bei Aktivierung von Apache Ranger können Sie einen Azure HDInsight-Cluster einrichten, der in eine mit Azure AD Domain Services verwaltete Domäne eingebettet ist. Erstellen und Anwenden von Hive-Richtlinien durch Apache Ranger, und Erteilen der Erlaubnis an Nutzer (z.B. Datenanalysten), sich mithilfe von ODBC-basierten Tools, z.B. Excel, Tableau usw. mit Hive zu verbinden. Microsoft arbeitet daran, schon bald andere Workloads, wie z.B. HBase, Spark und Storm zu in HDInsight mit Domänenverknüpfung hinzuzufügen.

Weitere Informationen zu diesem Bereitstellungsszenario finden Sie im Artikel zum [Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern](../hdinsight/domain-joined/apache-domain-joined-configure.md).
