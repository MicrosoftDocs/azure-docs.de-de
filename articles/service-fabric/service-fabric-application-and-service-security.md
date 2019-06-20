---
title: Informationen zur Azure Service Fabric-Anwendungssicherheit | Microsoft Docs
description: Ein Überblick über die sichere Ausführung von Microservicesanwendungen in Service Fabric. Erfahren Sie, wie Sie Dienste und das Startskript unter verschiedenen Sicherheitskonten ausführen, Benutzer authentifizieren und autorisieren, Anwendungsgeheimnisse verwalten, die Dienstkommunikation sichern, ein API-Gateway verwenden und ruhende Anwendungsdaten sichern.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: aljo
ms.openlocfilehash: cb0f750f4049a1ce652c829f43928a95f30e6973
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302243"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-Anwendungs- und -Dienstsicherheit
Eine Microservicesarchitektur kann [zahlreiche Vorteile](service-fabric-overview-microservices.md) mit sich bringen. Die Verwaltung der Sicherheit von Microservices ist jedoch eine Herausforderung und unterscheidet sich von der Verwaltung traditioneller monolithischer Anwendungen. 

Bei einer monolithischen Anwendung wird die Anwendung normalerweise auf mindestens einem Server in einem Netzwerk ausgeführt, und es ist einfacher, die verfügbar gemachten Ports und APIs sowie die IP-Adresse zu identifizieren. Es gibt häufig einen Umkreis oder eine Begrenzung, und es muss eine Datenbank geschützt werden. Wenn dieses System aufgrund einer Sicherheitsverletzung oder eines Angriffs kompromittiert wird, ist es wahrscheinlich, dass dem Angreifer innerhalb des Systems alle Informationen zur Verfügung stehen. Bei Microservices ist das System komplexer.  Die Dienste sind dezentralisiert und auf viele Hosts verteilt und migrieren von Host zu Host.  Mit der ordnungsgemäßen Sicherheit schränken Sie die Berechtigungen ein, die ein Angreifer erhalten kann, sowie die Datenmenge, die in einem einzigen Angriff verfügbar ist, wenn ein Dienst verletzt wird.  Die Kommunikation erfolgt nicht intern, sondern über ein Netzwerk, und es sind viele verfügbar gemachte Ports und Interaktionen zwischen den Diensten vorhanden. Zu wissen, was diese Dienstinteraktionen sind und wann sie stattfinden, ist entscheidend für die Anwendungssicherheit.

Dieser Artikel ist kein Leitfaden für die Sicherheit von Microservices (es sind viele solcher Ressourcen online verfügbar), sondern er beschreibt, wie verschiedene Aspekte der Sicherheit in Service Fabric erreicht werden können.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung
Es ist häufig erforderlich, die Verwendung von Ressourcen und APIs, die von einem Dienst verfügbar gemacht werden, auf bestimmte vertrauenswürdige Benutzer oder Clients einzuschränken. Authentifizierung ist der Vorgang, bei dem die Identität eines Benutzers zuverlässig ermittelt wird.  Autorisierung ist der Vorgang, durch den APIs oder Dienste einigen authentifizierten Benutzern, aber nicht allen zur Verfügung gestellt werden.

### <a name="authentication"></a>Authentication
Der erste Schritt, Entscheidungen zur Vertrauenswürdigkeit auf API-Ebene zu treffen, ist die Authentifizierung. Authentifizierung ist der Vorgang, bei dem die Identität eines Benutzers zuverlässig ermittelt wird.  Die Authentifizierung wird in Microserviceszenarien in der Regel zentral vorgenommen. Wenn Sie ein API-Gateway verwenden, können Sie [die Authentifizierung an das Gateway auslagern](/azure/architecture/patterns/gateway-offloading). Wenn Sie diesen Ansatz verwenden, stellen Sie sicher, dass die einzelnen Dienste nicht direkt (ohne das API-Gateway) erreicht werden können, es sei denn, es ist zusätzliche Sicherheit für die Authentifizierung von Nachrichten vorhanden, und zwar unabhängig davon, ob sie vom Gateway stammen oder nicht.

Wenn auf Dienste direkt zugegriffen werden kann, kann ein Authentifizierungsdienst wie Azure Active Directory oder ein dedizierter Authentifizierungsmicroservice verwendet werden, der als Sicherheitstokendienst (Security Token Service, STS) fungiert, um Benutzer zu authentifizieren. Vertrauensentscheidungen werden zwischen Diensten mit Sicherheitstoken oder Cookies geteilt. 

Für ASP.NET Core ist der primäre Mechanismus für die [Authentifizierung von Benutzern](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) das ASP.NET Core Identity-Mitgliedschaftssystem. ASP.NET Core Identity speichert Benutzerinformationen (einschließlich Anmeldeinformationen, Rollen und Ansprüche) in einem vom Entwickler konfigurierten Datenspeicher. ASP.NET Core Identity unterstützt zweistufige Authentifizierung.  Externe Authentifizierungsanbieter werden ebenfalls unterstützt, sodass Benutzer sich mit vorhandenen Authentifizierungsverfahren von Anbietern wie Microsoft, Google, Facebook oder Twitter anmelden können.

### <a name="authorization"></a>Autorisierung
Nach der Authentifizierung müssen Dienste den Benutzerzugriff autorisieren oder ermitteln, welche Aktionen ein Benutzer ausführen kann. Dieser Prozess ermöglicht es einem Dienst, APIs für einige authentifizierte Benutzer zur Verfügung zu stellen, aber nicht für alle. Die Autorisierung ist orthogonal und unabhängig von der Authentifizierung, d.h. der Feststellung, wer ein Benutzer ist. Die Authentifizierung kann mindestens eine Identität für den aktuellen Benutzer erstellen.

[ASP.NET Core-Autorisierung](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kann basierend auf den Rollen der Benutzer oder auf benutzerdefinierten Richtlinien ausgeführt werden, die die Überprüfung von Ansprüchen oder andere Heuristiken beinhalten können.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Einschränken und Sichern des Zugriffs mithilfe eines API-Gateways
Cloudanwendungen benötigen normalerweise ein Front-End-Gateway, um für Benutzer, Geräte oder andere Anwendungen einen zentralen Eingangspunkt bereitzustellen. Ein [API-Gateway](/azure/architecture/microservices/gateway) befindet sich zwischen Clients und Diensten und ist der Einstiegspunkt zu allen Diensten, die Ihre Anwendung bereitstellt. Es fungiert als Reverseproxy und leitet Anforderungen von Clients an Dienste weiter. Darüber hinaus kann es verschiedene übergreifende Aufgaben wie Authentifizierung und Autorisierung, SSL-Beendigung und Ratenbegrenzung übernehmen. Wenn Sie kein Gateway bereitstellen, müssen Clients Anforderungen direkt an Front-End-Dienste senden.

In Service Fabric kann ein Gateway ein beliebiger zustandsloser Dienst sein (etwa eine [ASP.NET Core-Anwendung](service-fabric-reliable-services-communication-aspnetcore.md)) oder ein anderer Dienst, der für den Eingang von Datenverkehr ausgelegt ist (etwa [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/) oder [Azure API Management](https://docs.microsoft.com/azure/api-management)).

API Management ist direkt in Service Fabric integriert, sodass Sie APIs mit einem umfassenden Satz von Routingregeln für Ihre Service Fabric-Back-End-Dienste veröffentlichen können.  Sie können den Zugriff auf Back-End-Dienste sichern, DOS-Angriffe durch Drosselung verhindern oder API-Schlüssel, JWT-Token, Zertifikate und andere Anmeldeinformationen überprüfen. Weitere Informationen finden Sie unter [Service Fabric mit Azure API Management: Übersicht](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Verwalten von Anwendungsgeheimnissen
Geheimnisse beinhalten jegliche Art von vertraulichen Informationen (z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen). In diesem Artikel wird Azure Key Vault für die Verwaltung von Schlüsseln und Geheimnissen verwendet. Die *Verwendung* von Geheimnissen in einer Anwendung ist jedoch cloudplattformunabhängig, sodass Anwendungen auf einem Cluster bereitgestellt werden können, der an einem beliebigen Standort gehostet wird.

Es wird empfohlen, Dienstkonfigurationseinstellungen über [Dienstkonfigurationspakete][config-package] zu verwalten. Konfigurationspakete verfügen über eine Versionsangabe und können über parallele Upgrades aktualisiert werden. Außerdem kann die Integrität überprüft und ein automatischer Rollback durchgeführt werden. Dies wird der globalen Konfiguration vorgezogen, da die Wahrscheinlichkeit eines globalen Dienstausfalls verringert wird. Verschlüsselte Geheimnisse stellen keine Ausnahme dar. Service Fabric verfügt über integrierte Features zum Verschlüsseln und Entschlüsseln von Werten in der Konfigurationspaketdatei „Settings.xml“ mithilfe der Zertifikatverschlüsselung.

Das Diagramm unten zeigt den grundlegenden Ablauf bei der Verwaltung von Geheimnissen in einer Service Fabric-Anwendung:

![Übersicht über die Verwaltung von Geheimnissen][overview]

Dieser Vorgang besteht im Wesentlichen aus vier Schritten:

1. Abrufen eines Datenverschlüsselungszertifikats
2. Installieren des Zertifikats in Ihrem Cluster
3. Verschlüsseln von Geheimnissen bei der Bereitstellung einer Anwendung mit dem Zertifikat und Einfügen dieser Geheimnisse in die Konfigurationsdatei „Settings.xml“ des Diensts
4. Lesen der verschlüsselten Werte aus der Datei „Settings.xml“, indem diese mit demselben Verschlüsselungszertifikat entschlüsselt werden 

[Azure Key Vault][key-vault-get-started] wird als sicherer Speicherort für Zertifikate sowie zum Installieren von Zertifikaten auf Service Fabric-Clustern in Azure verwendet. Wenn die Bereitstellung nicht in Azure erfolgt, muss Key Vault nicht zum Verwalten von Geheimnissen in Service Fabric-Anwendungen eingesetzt werden.

Ein Beispiel finden Sie unter [Verwalten von Anwendungsgeheimnissen](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Sichern der Hostumgebung
Durch Verwenden von Azure Service Fabric können Sie Anwendungen sichern, die im Cluster unter verschiedenen Benutzerkonten ausgeführt werden. Mit Service Fabric werden auch die Ressourcen gesichert, die von Anwendungen zum Zeitpunkt der Bereitstellung in den Benutzerkonten genutzt werden, z.B. Dateien, Verzeichnisse und Zertifikate. So lässt sich erreichen, dass ausgeführte Anwendungen auch in einer gemeinsamen gehosteten Umgebung sicher voneinander abgegrenzt sind.

Das Anwendungsmanifest deklariert die Sicherheitsprinzipale (Benutzer und Gruppen), die für die Ausführung der Dienste und das Sichern von Ressourcen erforderlich sind.  Auf diese Sicherheitsprinzipale wird in Richtlinien verwiesen, z.B. in den RunAs-, Endpunktbindungs-, Paketfreigabe- oder Sicherheitszugriffsrichtlinien.  Richtlinien werden dann im Abschnitt **ServiceManifestImport** des Anwendungsmanifests auf Dienstressourcen angewendet.

Beim Deklarieren von Prinzipalen können Sie auch Benutzergruppen definieren und erstellen, sodass jeder Gruppe ein oder mehrere Benutzer hinzugefügt werden können, die gemeinsam verwaltet werden sollen. Dies ist nützlich, wenn es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte allgemeine Berechtigungen benötigen.

Standardmäßig werden Service Fabric-Anwendungen unter dem Konto ausgeführt, unter dem der Prozess „Fabric.exe“ ausgeführt wird. Darüber hinaus bietet Service Fabric die Möglichkeit zur Ausführung von Anwendungen in einem lokalen Benutzer- oder Systemkonto, das im Manifest der Anwendung angegeben wird. Weitere Informationen finden Sie unter [Ausführen eines Diensts als lokales Benutzerkonto oder lokales Systemkonto](service-fabric-application-runas-security.md).  Sie können auch ein [Dienststartskript als lokaler Benutzer oder Systemkonto ausführen](service-fabric-run-script-at-service-startup.md).

Wenn Sie Service Fabric auf einem eigenständigen Windows-Cluster ausführen, können Sie einen Dienst unter [Active Directory-Domänenkonten](service-fabric-run-service-as-ad-user-or-group.md) oder [gruppenverwalteten Dienstkonten](service-fabric-run-service-as-gmsa.md) ausführen.

## <a name="secure-containers"></a>Sichere Container
Service Fabric bietet einen Mechanismus, über den Dienste innerhalb eines Containers auf ein Zertifikat zugreifen können, das auf den Knoten eines Windows- oder Linux-Clusters (ab Version 5.7) installiert ist. Dieses PFX-Zertifikat kann verwendet werden, um die Anwendung oder den Dienst zu authentifizieren oder die Kommunikation mit anderen Diensten zu schützen. Weitere Informationen finden Sie unter [Importieren eines Zertifikats in einen Container](service-fabric-securing-containers.md).

Für Windows-Container unterstützt Service Fabric zudem auch gruppenverwaltete Dienstkonten (group Managed Service Accounts, gMSAs). Weitere Informationen finden Sie unter [Einrichten von gMSA für Windows-Container](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Sichern der Dienstkommunikation
In Service Fabric wird ein Dienst an irgendeinem Ort in einem Service Fabric-Cluster ausgeführt, der sich in der Regel auf mehreren virtuellen Computern befindet. Service Fabric bietet mehrere Optionen zum Sichern der Dienstkommunikation.

Sie können HTTPS-Endpunkte in Ihren [ASP.NET Core- oder Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)-Webdiensten aktivieren.

Sie können eine sichere Verbindung zwischen dem Reverseproxy und Diensten herstellen und so einen sicheren End-to-End-Kanal ermöglichen. Die Herstellung einer Verbindung mit sicheren Diensten wird nur unterstützt, wenn der Reverseproxy für das Lauschen von HTTPS konfiguriert ist. Informationen zum Konfigurieren des Reverseproxys finden Sie unter [Reverseproxy in Azure Service Fabric](service-fabric-reverseproxy.md).  Unter [Herstellen einer Verbindung mit einem sicheren Dienst](service-fabric-reverseproxy-configure-secure-communication.md) wird beschrieben, wie eine sichere Verbindung zwischen dem Reverseproxy und Diensten hergestellt wird.

Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. Erfahren Sie, wie Sie die Sicherheit verbessern können, wenn Sie Dienstremoting (in [C#](service-fabric-reliable-services-secure-communication.md) oder [Java](service-fabric-reliable-services-secure-communication-java.md)) oder [WCF](service-fabric-reliable-services-secure-communication-wcf.md) verwenden.

## <a name="encrypt-application-data-at-rest"></a>Verschlüsseln von ruhenden Anwendungsdaten
Jedem [Knotentyp](service-fabric-cluster-nodetypes.md) in einem in Azure ausgeführten [Service Fabric](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)-Cluster liegt eine VM-Skalierungsgruppe zugrunde. Mit einer Azure Resource Manager-Vorlage können Sie Datenträger an die Skalierungsgruppen anfügen, aus denen sich der Service Fabric-Cluster zusammensetzt.  Wenn Ihre Dienste Daten auf einem angefügten Datenträger speichern, können Sie diese [Datenträger verschlüsseln](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), um Ihre Anwendungsdaten zu schützen.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* [Ausführen eines Setupskripts beim Dienststart](service-fabric-run-script-at-service-startup.md)
* [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)
* [Informationen zur Clustersicherheit](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png