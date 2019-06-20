---
title: Checkliste für die Azure Service Fabric-Sicherheit | Microsoft-Dokumentation
description: Dieser Artikel bietet einen Satz von Checklisten für die Azure Service Fabric-Sicherheit.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611540"
---
# <a name="azure-service-fabric-security-checklist"></a>Checkliste für die Azure Service Fabric-Sicherheit
Dieser Artikel enthält eine einfach zu verwendende Checkliste, die Sie zum Sichern Ihrer Azure Service Fabric-Umgebung nutzen können.

## <a name="introduction"></a>Einführung
Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht. Service Fabric bietet außerdem einfache Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von Cloudanwendungen. Entwickler und Administratoren können komplexe Infrastrukturprobleme vermeiden und sich auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind.

## <a name="checklist"></a>Checkliste
Verwenden Sie die folgende Checkliste, um sicherzustellen, dass Sie keine wichtigen Probleme bei der Verwaltung und Konfiguration einer sicheren Azure Service Fabric-Lösung übersehen haben.


|Checklistenkategorie| BESCHREIBUNG |
| ------------ | -------- |
|[Rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Zugriffssteuerung ermöglicht es dem Clusteradministrator, den Zugriff auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern einzuschränken, wodurch die Sicherheit des Clusters erhöht wird.</li><li>Administratoren haben vollständigen Zugriff auf Verwaltungsfunktionen (einschließlich Lese-/Schreibzugriff). </li><li> Benutzer haben standardmäßig nur Lesezugriff auf Verwaltungsfunktionen (z. B. Abfragefunktionen) sowie die Möglichkeit, Anwendungen und Dienste aufzulösen.</li></ul>|
|[X.509-Zertifikate und Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Zertifikate](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates), die in Clustern mit Produktionsworkloads verwendet werden, müssen entweder mit einem korrekt konfigurierten Windows Server-Zertifikatsdienst erstellt oder über eine genehmigte [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) bezogen werden.</li><li>Verwenden Sie in der Produktion niemals [temporäre oder Testzertifikate](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development), die mit Tools wie [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx) erstellt wurden. </li><li>Sie können ein [selbstsigniertes Zertifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) nur für Testcluster verwenden, aber nicht in der Produktion.</li></ul>|
|[Clustersicherheit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Zu den Szenarien für die Clustersicherheit gehören Knoten-zu-Knoten-Sicherheit, Client-zu-Knoten-Sicherheit und [rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Clusterauthentifizierung](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Authentifiziert die [Kommunikation zwischen Knoten](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) für einen Clusterverbund. </li></ul>|
|[Serverauthentifizierung](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Authentifiziert die [Verwaltungsendpunkte des Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) bei einem Verwaltungsclient.</li></ul>|
|[Anwendungssicherheit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Verschlüsselung und Entschlüsselung von Anwendungskonfigurationswerten</li><li>   Knotenübergreifende Verschlüsselung von Daten während der Replikation</li></ul>|
|[Clusterzertifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Dieses Zertifikat ist erforderlich, um die Kommunikation zwischen den Knoten in einem Cluster zu schützen.</li><li>    Legen Sie den Fingerabdruck des primären Zertifikats im Abschnitt „Thumbprint“ und den Fingerabdruck des sekundären Zertifikats in den Variablen unter „ThumbprintSecondary“ fest.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Dieses Zertifikat wird dem Client angezeigt, wenn versucht wird, eine Verbindung mit diesem Cluster herzustellen. Sie können zwei verschiedene Serverzertifikate verwenden: ein primäres Zertifikat und ein sekundäres Zertifikat für Upgrades.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Dies ist eine Gruppe von Zertifikaten, die auf den authentifizierten Clients installiert werden sollen. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Legen Sie den allgemeinen Namen des ersten Clientzertifikats für „CertificateCommonName“ fest. „CertificateIssuerThumbprint“ ist der Fingerabdruck für den Aussteller dieses Zertifikats. </li></ul>|
|ReverseProxyCertificate| <ul><li>Hierbei handelt es sich um ein optionales Zertifikat, das zum Schutz des [Reverseproxys](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) angegeben werden kann. </li></ul>|
|Key Vault| <ul><li>Dient zum Verwalten von Zertifikaten für Service Fabric-Cluster in Azure.  </li></ul>|


## <a name="next-steps"></a>Nächste Schritte

- [Bewährte Methoden für die Service Fabric-Sicherheit](azure-service-fabric-security-best-practices.md)
- [Service Fabric-Cluster-Upgradeprozess und Erwartungen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio)
- [Einführung in das Service Fabric-Integritätsmodell](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)
