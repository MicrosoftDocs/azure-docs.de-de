---
title: Konfigurieren von Windows-Java-Apps – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Java-Apps so konfigurieren, dass sie auf den Windows-Standardinstanzen in Azure App Service ausgeführt werden können.
keywords: Azure App Service, Web-App, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604147"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurieren einer Windows-Java-App für Azure App Service

Mit Azure App Service können Java-Entwickler schnell ihre mit Tomcat oder Java Standard Edition (SE) paketierten Webanwendungen in einem vollständig verwalteten Windows-basierten Dienst erstellen, bereitstellen und skalieren. Stellen Sie Anwendungen mit Maven-Plug-Ins über die Befehlszeile oder in Editoren wie IntelliJ, Eclipse oder Visual Studio Code bereit.

Dieser Leitfaden enthält die wichtigsten Konzepte und Anweisungen für Java-Entwickler, die App Service verwenden. Wenn Sie Azure App Service noch nie verwendet haben, lesen Sie zunächst den [Java-Schnellstart](app-service-web-get-started-java.md). Allgemeine Fragen zur Verwendung von App Service, die sich nicht speziell auf die Java-Entwicklung beziehen, werden unter [Häufig gestellte Fragen (FAQ) zu App Service unter Windows](faq-configuration-and-management.md) beantwortet.

> [!NOTE]
> Können Sie die gesuchten Informationen nicht finden? Informationen zum Bereitstellen und Absichern Ihrer Java-App finden Sie in den [häufig gestellten Fragen (FAQ) zu OSS unter Windows](faq-configuration-and-management.md) und im [Java-Linux-Konfigurationshandbuch](containers/configure-language-java.md).

## <a name="configuring-tomcat"></a>Konfigurieren von Tomcat

Um die Datei `server.xml` oder andere Konfigurationsdateien von Tomcat zu bearbeiten, notieren Sie zuerst Ihre Tomcat-Hauptversion im Portal.

1. Suchen Sie das Tomcat-Stammverzeichnis für Ihre Version, indem Sie den Befehl `env` ausführen. Suchen Sie nach der Umgebungsvariable, die mit `AZURE_TOMCAT` beginnt und Ihrer Hauptversion entspricht. Beispielsweise verweist `AZURE_TOMCAT85_HOME` auf das Tomcat-Verzeichnis für Tomcat 8.5.
1. Nachdem Sie das Tomcat-Stammverzeichnis für Ihre Version identifiziert haben, kopieren Sie das Konfigurationsverzeichnis nach `D:\home`. Wenn beispielsweise `AZURE_TOMCAT85_HOME` den Wert `D:\Program Files (x86)\apache-tomcat-8.5.37` aufweist, lautet der vollständige Pfad des kopierten Konfigurationsverzeichnisses `D:\home\tomcat\conf`.

Starten Sie dann Ihre App Service-Instanz neu. Ihre Bereitstellungen sollten wie vorher in `D:\home\site\wwwroot\webapps` ausgeführt werden.

## <a name="java-runtime-statement-of-support"></a>Unterstützungserklärung für die Java-Runtime

### <a name="jdk-versions-and-maintenance"></a>JDK-Versionen und -Wartung

Als Java Development Kit (JDK) wird das von [Azul Systems](https://www.azul.com/) bereitgestellte [Zulu](https://www.azul.com/downloads/azure-only/zulu/) von Azure unterstützt.

Größere Versionsupdates werden durch neue Runtimeoptionen in Azure App Service für Windows bereitgestellt. Kunden führen das Update auf diese neueren Versionen von Java durch die Konfiguration ihrer App Service-Bereitstellung durch und müssen durch Tests sicherstellen, dass das größere Update ihren Anforderungen entspricht.

Unterstützte JDKs werden jedes Vierteljahr im Januar, April, Juli und Oktober automatisch gepatcht.

### <a name="security-updates"></a>Sicherheitsupdates

Patches und Fixes für größere Sicherheitsrisiken werden veröffentlicht, sobald sie von Azul Systems zur Verfügung gestellt werden. Ein „größeres“ Sicherheitsrisiko wird durch eine Gesamtbewertung von 9.0 oder höher im [NIST Common Vulnerability Scoring System, Version 2](https://nvd.nist.gov/cvss.cfm), definiert.

### <a name="deprecation-and-retirement"></a>Einstellung und Außerbetriebnahme

Wenn eine unterstützte Java-Runtime eingestellt wird, erhalten Azure-Entwickler, die die betreffende Runtime verwenden, mindestens sechs Monate vor Außerbetriebnahme der Runtime eine entsprechende Benachrichtigung.

### <a name="local-development"></a>Lokale Entwicklung

Entwickler können die Production Edition des Azul Zulu Enterprise JDK zur lokalen Entwicklung von der [Azul-Downloadsite](https://www.azul.com/downloads/azure-only/zulu/) herunterladen.

### <a name="development-support"></a>Entwicklungsunterstützung

Produktsupport für das von [Azure unterstützte Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) ist bei der Entwicklung für Azure oder [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) mit einem [qualifizierten Azure-Supportplan](https://azure.microsoft.com/support/plans/) von Microsoft erhältlich.

### <a name="runtime-support"></a>Laufzeitunterstützung

Entwickler können ein [Problem](/azure/azure-supportability/how-to-create-azure-support-request) mit den Azul Zulu JDKs über den Azure-Support melden, wenn sie einen [qualifizierten Supportplan](https://azure.microsoft.com/support/plans/) besitzen.

## <a name="next-steps"></a>Nächste Schritte

Dieses Thema behandelt die Java Runtime-Unterstützungsanweisung für Azure App Service unter Windows.

- Weitere Informationen zum Hosten von Webanwendungen mit Azure App Service finden Sie in der [App Service-Übersicht](overview.md).
- Weitere Informationen zur Java-Entwicklung in Azure finden Sie unter [Azure für Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
