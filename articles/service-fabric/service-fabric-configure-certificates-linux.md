---
title: Konfigurieren von Zertifikaten für Azure Service Fabric-Anwendungen unter Linux | Microsoft-Dokumentation
description: Konfigurieren von Zertifikaten für Ihre App mit der Service Fabric-Runtime in einem Linux-Cluster
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: c0580b75544a9613bc8caf2faaac11ba1ba6708e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881367"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Zertifikate und Sicherheit in Linux-Clustern

Dieser Artikel enthält Informationen zur Konfiguration von X.509-Zertifikaten in Linux-Clustern.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Speicherort und Format von X.509-Zertifikaten auf Linux-Knoten

Service Fabric erwartet normalerweise, dass X.509-Zertifikate im Verzeichnis */var/lib/sfcerts* von Linux-Clusterknoten vorhanden sind. Dies gilt für Clusterzertifikate, Clientzertifikate usw. In einigen Fällen können Sie für Zertifikate auch einen anderen Speicherort als den Ordner *var/lib/sfcerts* angeben. Mit Reliable Services, die mit dem Service Fabric-Java-SDK erstellt werden, können Sie beispielsweise über das Konfigurationspaket (Settings.xml) einen anderen Speicherort für einige anwendungsspezifische Zertifikate angeben. Weitere Informationen finden Sie unter [Im Konfigurationspaket (Settings.xml) angegebene Zertifikate](#certificates-referenced-in-the-configuration-package-settingsxml).

Für Linux-Cluster erwartet Service Fabric, dass Zertifikate entweder als PEM-Datei (die sowohl das Zertifikat als auch den privaten Schlüssel enthält) oder als CRT-Datei (die das Zertifikat und eine KEY-Datei mit dem privaten Schlüssel enthält) vorliegen. Alle Dateien sollten im PEM-Format vorhanden sein. 

Wenn Sie Ihr Zertifikat über Azure Key Vault installieren, indem Sie entweder eine [Resource Manager-Vorlage](./service-fabric-cluster-creation-create-template.md) oder [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric)-Befehle verwenden, wird das Zertifikat auf jedem Knoten im richtigen Format im Verzeichnis */var/lib/sfcerts* installiert. Wenn Sie ein Zertifikat mit einer anderen Methode installieren, müssen Sie sicherstellen, dass das Zertifikat richtig auf den Clusterknoten installiert ist.

## <a name="certificates-referenced-in-the-application-manifest"></a>Im Anwendungsmanifest angegebene Zertifikate

Im Anwendungsmanifest angegebene Zertifikate (z.B. über die Elemente [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) oder [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)) müssen im Verzeichnis */var/lib/sfcerts* vorhanden sein. Für die Elemente, die zum Angeben von Zertifikaten im Anwendungsmanifest verwendet werden, wird kein Pfadattribut verwendet. Daher müssen die Zertifikate im Standardverzeichnis enthalten sein. Für diese Elemente wird optional das Attribut **X509StoreName** verwendet. Mit dem Standardwert „My“ wird auf Linux-Knoten auf das Verzeichnis */var/lib/sfcerts* verwiesen. Alle anderen Werte sind in einem Linux-Cluster nicht definiert. Wir empfehlen Ihnen, das Attribut **X509StoreName** für Apps wegzulassen, die in Linux-Clustern ausgeführt werden. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Im Konfigurationspaket (Settings.xml) angegebene Zertifikate

Für einige Dienste können Sie X.509-Zertifikate unter [ConfigPackage](./service-fabric-application-and-service-manifests.md) (standardmäßig „Settings.xml“) konfigurieren. Dies ist beispielsweise der Fall, wenn Sie Zertifikate zum Schützen von RPC-Kanälen für Reliable Services-Dienste deklarieren, die per Service Fabric .NET Core oder Java-SDK erstellt wurden. Es gibt zwei Möglichkeiten, im Konfigurationspaket auf Zertifikate zu verweisen. Die Unterstützung unterscheidet sich für .NET Core und Java-SDKs.

### <a name="using-x509-securitycredentialstype"></a>Verwenden von X509 für SecurityCredentialsType

Bei .NET oder Java-SDKs können Sie für **SecurityCredentialsType** die Option **X509** angeben. Dies entspricht dem Typ `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) von `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

Mit dem Verweis **X509** wird das Zertifikat in einem Zertifikatspeicher ermittelt. Im folgenden XML-Code sind die Parameter dargestellt, die zum Angeben des Zertifikatspeicherorts verwendet werden:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Für einen unter Linux ausgeführten Dienst verweist **LocalMachine**/**My** auf den Standardspeicherort für Zertifikate: das Verzeichnis */var/lib/sfcerts*. Für Linux sind alle anderen Kombinationen von **CertificateStoreLocation** und **CertificateStoreName** nicht definiert. 

Geben Sie für den Parameter **CertificateStoreLocation** immer **LocalMachine** an. Es ist nicht erforderlich, den Parameter **CertificateStoreName** anzugeben, da hierfür der Standardwert „My“ verwendet wird. Beim Verweis **X509** müssen sich die Zertifikatdateien im Verzeichnis */var/lib/sfcerts* auf dem Clusterknoten befinden.  

Der folgende XML-Code enthält den Abschnitt **TransportSettings**, der auf diesem Stil basiert:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>Verwenden von X509_2 als SecurityCredentialsType

Mit dem Java-SDK können Sie für **SecurityCredentialsType** den Wert **X509_2** angeben. Dies entspricht dem Typ `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) von `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Beim Verweis **X509_2** geben Sie einen Pfadparameter an, damit Sie das Zertifikat in einem anderen Verzeichnis als */var/lib/sfcerts* ermitteln können.  Im folgenden XML-Code sind die Parameter dargestellt, die zum Angeben des Zertifikatspeicherorts verwendet werden: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Der folgende XML-Code enthält den Abschnitt **TransportSettings**, der auf diesem Stil basiert.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Das Zertifikat wird im obigen XML-Code als CRT-Datei angegeben. Dies impliziert, dass an demselben Speicherort auch eine KEY-Datei mit dem privaten Schlüssel vorhanden ist.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurieren einer Reliable Services-App zur Ausführung in Linux-Clustern

Mit den Service Fabric SDKs können Sie mit den Service Fabric-Runtime-APIs kommunizieren, um die Plattform zu nutzen. Wenn Sie eine beliebige Anwendung ausführen, für die diese Funktionalität in sicheren Linux-Clustern verwendet wird, müssen Sie Ihre Anwendung mit einem Zertifikat konfigurieren, die diese für die Überprüfung per Service Fabric-Runtime nutzen kann. Für Anwendungen, die Service Fabric-Reliable Service-Dienste (per .NET Core oder Java SDK geschrieben) enthalten, ist diese Konfiguration erforderlich. 

Fügen Sie zum Konfigurieren einer Anwendung ein [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element)-Element unter dem **Certificates**-Tag hinzu, das sich unter dem **ApplicationManifest**-Tag in der Datei *ApplicationManifest.xml* befindet. Der folgende XML-Code enthält ein Zertifikat, auf das über seinen Fingerabdruck verwiesen wird: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Sie können entweder auf das Clusterzertifikat oder auf ein Zertifikat verweisen, das Sie auf den einzelnen Knoten installieren. Unter Linux müssen sich die Zertifikatdateien im Verzeichnis */var/lib/sfcerts* befinden. Weitere Informationen finden Sie unter [Speicherort und Format von X.509-Zertifikaten auf Linux-Knoten](#location-and-format-of-x509-certificates-on-linux-nodes).



