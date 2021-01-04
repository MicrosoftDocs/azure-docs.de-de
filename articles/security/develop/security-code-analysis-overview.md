---
title: Übersicht zur Dokumentation der Microsoft-Sicherheitscodeanalyse
description: Erfahren Sie mehr über die Erweiterung „Microsoft-Sicherheitscodeanalyse“. Mit dieser Erweiterung können Sie die Sicherheitscodeanalyse in Azure DevOps-CI- und -ID-Pipelines integrieren.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a3af3307892e82ecd0697124954e99837ad5eb9e
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515692"
---
# <a name="about-microsoft-security-code-analysis"></a>Informationen zur Microsoft-Sicherheitscodeanalyse

Mit der Erweiterung „Microsoft-Sicherheitscodeanalyse“ können Teams die Sicherheitscodeanalyse nahtlos in ihre Azure DevOps-CI/CD (Continuous Integration und Continuous Delivery)-Pipelines integrieren. Diese Analyse wird von [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices)-Experten bei Microsoft empfohlen.

Die Sicherheit wird durch eine konsistente Benutzeroberfläche vereinfacht, die die Komplexität ausgeführter Tools abstrahiert. Mit einer NuGet-basierten Bereitstellung der Tools müssen Teams die Installation oder Aktualisierung der Tools nicht mehr verwalten. Über Befehlszeilen und Standardschnittstellen für Buildtasks können Benutzer das gewünschte Maß an Kontrolle über die Tools ausüben.

Teams können auch leistungsfähige Funktionen für die Nachbearbeitung nutzen wie

- Veröffentlichen von Protokollen zur Aufbewahrung
- Generieren umsetzbarer und entwicklerorientierter Berichte
- Konfigurieren von Buildunterbrechungen bei Regressionstests

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Gründe für die Verwendung der Microsoft-Sicherheitscodeanalyse

### <a name="security-simplified"></a>Vereinfachte Sicherheit

Tools der Microsoft-Sicherheitscodeanalyse werden der Azure DevOps-Pipeline genauso einfach hinzugefügt wie neue Tasks. Passen Sie die Tasks an, oder verwenden Sie die Standardeinstellungen. Tasks werden als Teil der Azure DevOps-Pipeline ausgeführt und generieren Protokolle, in denen alle möglichen Arten von Ergebnissen aufgeführt sind.

### <a name="clean-builds"></a>Bereinigte Builds

Nachdem Sie die anfänglichen Probleme behoben haben, die von den Tools gemeldet wurden, können Sie die Erweiterung so konfigurieren, dass die Builds bei neuen Problemen unterbrochen werden.  Das Einrichten von Continuous Integration-Builds bei jedem Pull Request ist ausgesprochen einfach.

### <a name="set-it-and-forget-it"></a>Einmal festlegen und dann vergessen

Die Buildtasks und -tools bleiben standardmäßig auf dem neuesten Stand. Wenn es eine aktualisierte Version eines Tools gibt, muss diese nicht heruntergeladen und installiert werden. Dies erfolgt automatisch über die Erweiterung.

### <a name="under-the-hood"></a>Hinter den Kulissen

Die Komplexität folgender Vorgänge wird durch die Buildtasks der Erweiterung abstrahiert:
  - Ausführen statischer Sicherheitsanalysetools
  - Verarbeiten der Ergebnisse aus Protokolldateien zum Erstellen eines Zusammenfassungsberichts oder Unterbrechen des Builds

## <a name="microsoft-security-code-analysis-tool-set"></a>Toolset der Microsoft-Sicherheitscodeanalyse

Mit der Erweiterung „Microsoft-Sicherheitscodeanalyse“ stehen Ihnen die neuesten Versionen wichtiger Analysetools zur Verfügung. Die Erweiterung umfasst sowohl von Microsoft verwaltete Tools als auch Open-Source-Tools.

Die Tools werden automatisch auf den in der Cloud gehosteten Agent heruntergeladen, nachdem Sie die Pipeline mithilfe des entsprechenden Buildtasks konfiguriert und ausgeführt haben.

Im folgenden Abschnitt sind die Tools aufgeführt, die derzeit in der Erweiterung verfügbar sind. Bleiben Sie auf dem Laufenden, und senden Sie uns Vorschläge für Tools, die hinzugefügt werden sollen.

### <a name="anti-malware-scanner"></a>Schadsoftwarescanner

Der Buildtask für den Schadsoftwarescanner ist nun in der Erweiterung „Microsoft-Sicherheitscodeanalyse“ enthalten. Er muss auf einem Build-Agent ausgeführt werden, auf dem Windows Defender bereits installiert ist. Weitere Informationen finden Sie auf der [Windows Defender-Website](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim ist ein einfacher PE-Scanner (Portable Executable, portierbar ausführbar), mit dem Compiler- und Linker-Einstellungen sowie andere sicherheitsrelevante Merkmale binärer Dateien überprüft werden. Durch den Buildtask wird ein Befehlszeilenwrapper für die Konsolenanwendung „BinSkim.exe“ erstellt. BinSkim ist ein Open-Source-Tool. Weitere Informationen finden Sie unter [BinSkim auf GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Credential Scanner

Kennwörter und andere Geheimnisse, die im Quellcode gespeichert sind, stellen ein erhebliches Problem dar. Credential Scanner ist ein proprietäres statisches Analysetool, mit dem das Problem behoben werden kann. Das Tool erkennt Anmeldeinformationen, Geheimnisse, Zertifikate und andere vertrauliche Inhalte im Quellcode und in der Buildausgabe.

### <a name="roslyn-analyzers"></a>Roslyn Analyzers

Roslyn Analyzers ist das in den Compiler integrierte statische Microsoft-Analysetool zum Analysieren von verwaltetem Code (C# und Visual Basic). Weitere Informationen finden Sie unter [Die auf Roslyn basierenden Analysetools](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

Bei TSLint handelt es sich um ein erweiterbares statisches Analysetool, mit dem TypeScript-Code auf Lesbarkeit, Wartungsfähigkeit und Funktionsfehler geprüft wird. Es wird von den meisten modernen Editoren und Buildsystemen unterstützt. Sie können das Tool mit Ihren eigenen Lint-Regeln, -Konfigurationen und -Formatierern anpassen. TSLint ist ein Open-Source-Tool. Weitere Informationen finden Sie unter [TSLint auf GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analyse und Nachbearbeitung von Ergebnissen

Die Erweiterung „Microsoft-Sicherheitscodeanalyse“ umfasst außerdem drei hilfreiche Nachbearbeitungstasks, mit denen Sie die von den Sicherheitstool-Tasks gefundenen Ergebnisse analysieren können. Wenn sie einer Pipeline hinzugefügt werden, werden sie in der Regel nach allen anderen Tooltasks ausgeführt.

### <a name="publish-security-analysis-logs"></a>Veröffentlichen von Sicherheitsanalyseprotokollen

Über den Buildtask zum Veröffentlichen von Sicherheitsanalyseprotokollen werden die Protokolldateien der während des Builds ausgeführten Sicherheitstools beibehalten. Die Protokolle können zu Untersuchungs- und Nachverfolgungszwecken gelesen werden.

Die Protokolldateien können als ZIP-Datei in Azure Artifacts veröffentlicht oder über den privaten Build-Agent in eine zugängliche Dateifreigabe kopiert werden.

### <a name="security-report"></a>Sicherheitsbericht

Mit dem Buildtask für den Sicherheitsbericht werden die Protokolldateien analysiert. Diese Dateien werden von den Sicherheitstools erstellt, die während des Builds ausgeführt werden. Anschließend wird vom Buildtask eine Zusammenfassungsberichtsdatei erstellt, die alle von den Analysetools gefundenen Probleme enthält.

Der Task kann so konfiguriert werden, dass Ergebnisse für ausgewählte oder für alle Tools erfasst werden. Außerdem können Sie festlegen, welche Fehlerstufe (Fehler oder Fehler und Warnungen) gemeldet wird.

### <a name="post-analysis-build-break"></a>Nachanalyse (Buildunterbrechung)

Mit dem Buildtask für die Nachanalyse kann der Kunde eine Buildunterbrechung einfügen, sodass der Build mit einem Fehler abgebrochen wird. Eine Buildunterbrechung wird eingefügt, wenn Codeprobleme durch ein oder mehrere Analysetools gemeldet werden.

Sie können diesen Task so konfigurieren, dass der Build bei Problemen, die von bestimmten Tools oder allen Tools gefunden werden, abgebrochen wird. Außerdem können Sie den Task basierend auf dem Schweregrad der gefundenen Probleme konfigurieren (z. B. Fehler oder Warnungen).

>[!NOTE]
>Standardmäßig wird jeder Buildtask ausgeführt, wenn der Task erfolgreich abgeschlossen wurde. Dies gilt unabhängig davon, ob ein Tool Probleme erkennt oder nicht. Folglich kann der Build bis zum Ende ausgeführt werden, indem die Ausführung aller Tools zugelassen wird.

## <a name="next-steps"></a>Nächste Schritte

Anweisungen zum Onboarding und zur Installation der Microsoft-Sicherheitscodeanalyse finden Sie in der Anleitung zu [Onboarding und Installation](security-code-analysis-onboard.md).

Weitere Informationen zum Konfigurieren der Buildtasks finden Sie in der [Konfigurationsanleitung](security-code-analysis-customize.md) oder im [YAML-Konfigurationshandbuch](yaml-configuration.md).

Wenn Sie weitere Fragen zur Erweiterung und zu den angebotenen Tools haben, lesen Sie unsere [Seite mit FAQs](security-code-analysis-faq.md).