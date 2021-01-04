---
title: Anpassen der Tasks der Microsoft-Sicherheitscodeanalyse
titleSuffix: Azure
description: In diesem Artikel wird beschrieben, wie die Tasks in der Erweiterung „Microsoft-Sicherheitscodeanalyse“ angepasst werden.
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
ms.openlocfilehash: 4016e1dd055b45f9cd59a172d0e71ef95fec1c40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008683"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurieren und Anpassen der Buildtasks

In diesem Artikel werden die in den einzelnen Buildtasks verfügbaren Konfigurationsoptionen ausführlich beschrieben. Zunächst werden in diesem Artikel die Tasks für die Tools der Sicherheitscodeanalyse erläutert. Zum Schluss werden die Tasks für die Nachbearbeitung beschrieben.

## <a name="anti-malware-scanner-task"></a>Schadsoftwarescanner-Task

>[!NOTE]
> Der Schadsoftwarescanner-Buildtask erfordert einen Build-Agent mit aktiviertem Windows Defender. Ein solcher Agent ist ab Hosted Visual Studio 2017 verfügbar. Der Buildtask kann nicht unter Verwendung des gehosteten Agents von Visual Studio 2015 ausgeführt werden.
>
> Obwohl Signaturen für diese Agents nicht aktualisiert werden können, sollten sie immer weniger als drei Stunden alt sein.

Details zur Taskkonfiguration finden Sie im folgenden Screenshot und Text.

![Konfigurieren des Schadsoftwarescanner-Buildtasks](./media/security-tools/5-add-anti-malware-task600.png)

Im Screenshot ist im Listenfeld **Type** (Typ) die Option **Basic** (Standard) ausgewählt. Wählen Sie **Custom** (Benutzerdefiniert) aus, um Befehlszeilenargumente zur Anpassung der Überprüfung anzugeben.

Windows Defender verwendet den Windows Update-Client, um Signaturen herunterzuladen und zu installieren. Wenn beim Signaturupdate für den Build-Agent ein Fehler auftritt, wird der **HRESULT**-Fehlercode wahrscheinlich durch Windows Update generiert.

Weitere Informationen zu Windows Update-Fehlern und dazu, wie Sie sie vermeiden, finden Sie unter [Windows Update-Fehlercodes nach Komponente](/windows/deployment/update/windows-update-error-reference) und im TechNet-Artikel zu [Fehlercodes des Windows Update-Agents](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für die Antischadsoftware](yaml-configuration.md#anti-malware-scanner-task).

## <a name="binskim-task"></a>BinSkim-Task

> [!NOTE]
> Bevor Sie den BinSkim-Task ausführen können, muss Ihr Build eine der folgenden Bedingungen erfüllen:
>  - Der Build erstellt binäre Artefakte aus verwaltetem Code.
>  - Sie haben binäre Artefakte committet, die Sie mit BinSkim analysieren möchten.

Details zur Taskkonfiguration finden Sie im folgenden Screenshot und in der Liste.

![Konfigurieren des BinSkim-Buildtasks](./media/security-tools/7-binskim-task-details.png)

- Legen Sie die Buildkonfiguration auf „Debug“ (Debuggen) fest, um PDB-Debugdateien zu erzeugen. BinSkim verwendet diese Dateien, um Probleme in den Ausgabebinärdateien wieder dem Quellcode zuzuordnen.
- So müssen Sie keine eigene Befehlszeile entwickeln und erstellen:
     - Wählen Sie in der Liste **Type** (Typ) die Option **Basic** (Standard) aus.
     - Wählen Sie in der Liste **Function** (Funktion) die Option **Analyze** (Analysieren) aus.
- Geben Sie unter **Target** (Ziel) mindestens einen Spezifizierer für ein Datei-, Verzeichnis- oder Filtermuster ein. Die Spezifizierer ergeben mindestens eine zu analysierende Binärdatei:
    - Mehrere Ziele müssen durch ein Semikolon (;) getrennt werden.
    - Ein Spezifizierer kann aus einer einzelnen Datei bestehen oder Platzhalter enthalten.
    - Verzeichnisangaben müssen immer mit „\\*“ enden.
    - Beispiele:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Wenn Sie in der Liste **Type** (Typ) die Option **Command Line** (Befehlszeile) auswählen, müssen Sie „binskim.exe“ ausführen:
     - Stellen Sie sicher, dass es sich beim ersten Argument für „binskim.exe“ um das Verb **analyze** gefolgt von mindestens einer Pfadangabe handelt. Jeder Pfad kann entweder ein vollständiger Pfad oder ein Pfad relativ zum Quellverzeichnis sein.
     - Mehrere Zielpfade müssen durch ein Leerzeichen getrennt werden.
     - Sie können die Option **/o** oder **/output** weglassen. Der Ausgabewert wird für Sie hinzugefügt oder ersetzt.
     - Standardmäßige Befehlszeilenkonfigurationen sehen wie folgt aus.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> Die nachstehenden Zeichen „\\*“ sind wichtig, wenn Sie Verzeichnisse für das Ziel angeben.

Weitere Informationen zu BinSkim-Befehlszeilenargumenten, Regeln nach ID oder Exitcodes finden Sie im [BinSkim-Benutzerhandbuch](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für BinSkim](yaml-configuration.md#binskim-task).

## <a name="credential-scanner-task"></a>Credential Scanner-Task

Details zur Taskkonfiguration finden Sie im folgenden Screenshot und in der Liste.

![Konfigurieren des Credential Scanner-Buildtasks](./media/security-tools/3-taskdetails.png)

Folgende Optionen sind verfügbar:
  - **Anzeigename**: Name des Azure DevOps-Tasks. Der Standardwert ist „Credential Scanner ausführen“.
  - **Hauptversion des Tools**: Verfügbare Werte sind **CredScan V2**, **CredScan V1**. Wir empfehlen Kunden, die Version **CredScan V2** zu verwenden.
  - **Output Format** (Ausgabeformat): Zu den verfügbaren Werten gehören **TSV**, **CSV**, **SARIF** und **PREfast**.
  - **Tool Version** (Toolversion): Es wird empfohlen, **Latest** (Neueste) auszuwählen.
  - **Scan Folder** (Überprüfungsordner): Der zu überprüfende Repositoryordner.
  - **Searchers File Type** (Suchroutinen-Dateityp): Die Optionen zum Suchen der für die Überprüfung verwendeten Suchroutinendatei.
  - **Suppressions File** (Unterdrückungsdatei): Eine [JSON-Datei](https://json.org/) kann zum Unterdrücken von Problemen im Ausgabeprotokoll verwendet werden. Weitere Informationen zu Unterdrückungsszenarien finden Sie im Abschnitt „FAQ“ dieses Artikels.
  - **Verbose Output** (Ausführliche Ausgabe): Selbsterklärend
  - **Batch Size** (Batchgröße): Die Anzahl gleichzeitiger Threads, die zum Ausführen von Credential Scanner verwendet werden. Der Standardwert lautet 20. Mögliche Werte reichen von 1 bis 2.147.483.647.
  - **Match Timeout** (Übereinstimmungstimeout): Gibt an, wie viele Sekunden die Suchroutine versucht, eine Übereinstimmung zu finden, bevor die Überprüfung abgebrochen wird.
  - **File Scan Read Buffer Size** (Lesepuffergröße der Dateiüberprüfung): Die Größe des beim Lesen von Inhalt verwendeten Puffers in Bytes. Der Standardwert lautet 524.288.  
  - **Maximum File Scan Read Bytes** (Maximale Anzahl gelesener Bytes der Dateiüberprüfung): Die maximale Anzahl von Bytes, die während der Inhaltsanalyse aus einer Datei gelesen werden. Der Standardwert lautet 104.857.600.
  - **Control Options (Steuerungsoptionen)**  > **Run this task (Diesen Task ausführen)** : Gibt an, wann der Task ausgeführt wird. Wählen Sie **Custom Conditions** (Benutzerdefinierte Bedingungen) aus, um komplexere Bedingungen festzulegen.
  - **Version**: Die Buildtaskversion in Azure DevOps. Diese Option wird selten verwendet.

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für Credential Scanner](yaml-configuration.md#credential-scanner-task).

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers-Task

> [!NOTE]
> Ihr Build muss folgende Bedingungen erfüllen, bevor Sie den Roslyn Analyzers-Task ausführen können:
>
> - Ihre Builddefinition enthält den integrierten MSBuild- oder VSBuild-Buildtask zum Kompilieren von C#- oder Visual Basic-Code. Der Analyzers-Task nutzt die Eingabe und Ausgabe des integrierten Tasks, um die MSBuild-Kompilierung mit aktivierten Roslyn Analyzers auszuführen.
> - Für den zur Ausführung dieses Buildtasks verwendeten Build-Agent ist Visual Studio 2017, Version 15.5 oder höher, installiert, sodass mindestens Compilerversion 2.6 genutzt wird.

Details zur Taskkonfiguration finden Sie in der folgenden Liste und im Hinweis.

Folgende Optionen sind verfügbar:

- **Ruleset** (Regelsatz): Die Werte sind **SDL Required** (SDL erforderlich), **SDL Recommended** (SDL empfohlen) oder Ihr eigener benutzerdefinierter Regelsatz.
- **Analyzers Version** (Analyzers-Version): Es wird empfohlen, **Latest** (Neueste) auszuwählen.
- **Compiler Warnings Suppressions File** (Unterdrückungsdatei für Compilerwarnungen): Eine Textdatei mit einer Liste von Warnungs-IDs, die unterdrückt werden.
- **Control Options (Steuerungsoptionen)**  > **Run this task (Diesen Task ausführen)** : Gibt an, wann der Task ausgeführt wird. Wählen Sie **Custom conditions** (Benutzerdefinierte Bedingungen) aus, um komplexere Bedingungen festzulegen.

> [!NOTE]
>
> - Roslyn Analyzers sind in den Compiler integriert und können nur im Rahmen der Kompilierung von „csc.exe“ ausgeführt werden. Daher muss für diesen Task der Compilerbefehl, der zuvor im Build ausgeführt wurde, wiedergegeben oder erneut ausgeführt werden. Bei der Wiedergabe oder Ausführung werden die Protokolle des MSBuild-Buildtasks von Azure DevOps (früher Visual Studio Team Services) abgefragt.
>
>   Es gibt keine andere Möglichkeit für den Task, die Befehlszeile für die MSBuild-Kompilierung zuverlässig aus der Builddefinition abzurufen. Wir haben überlegt, ein Freiform-Textfeld hinzuzufügen, in dem Benutzer ihre Befehlszeilen eingeben können. Dann wäre es allerdings schwierig, diese Befehlszeilen auf dem neuesten Stand und synchron mit dem Hauptbuild zu halten.
>
>   Bei benutzerdefinierten Builds muss der gesamte Befehlssatz wiedergegeben werden, nicht nur Compilerbefehle. In diesen Fällen ist die Aktivierung von Roslyn Analyzers weder einfach noch zuverlässig.
>
> - Roslyn Analyzers sind in den Compiler integriert. Roslyn Analyzers müssen kompiliert werden, damit sie aufgerufen werden können.
>
>   Der neue Buildtask wird implementiert, indem C#-Projekte, die bereits erstellt wurden, erneut kompiliert werden. Der neue Task verwendet nur den MSBuild-und VSBuild-Buildtask im selben Build oder in derselben Builddefinition wie der ursprüngliche Task. In diesem Fall werden sie vom neuen Task jedoch mit aktivierten Roslyn Analyzers verwendet.
>
>   Wenn der neue Task auf dem selben Agent wie der ursprüngliche Task ausgeführt wird, wird die Ausgabe des ursprünglichen Tasks im Quellordner *s* durch die Ausgabe des neuen Tasks überschrieben. Obwohl die Buildausgabe identisch ist, wird empfohlen, dass Sie MSBuild ausführen, die Ausgabe in das Artefaktstagingverzeichnis kopieren und dann Roslyn Analyzers ausführen.

Weitere Ressourcen für den Roslyn Analyzers-Task finden Sie unter [Die auf Roslyn basierenden Analysetools](/dotnet/standard/analyzers/api-analyzer) in der Microsoft-Dokumentation.

Sie finden das Analyzer-Paket, das von diesem Buildtask installiert und verwendet wird, auf der NuGet-Seite [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für Roslyn Analyzers](yaml-configuration.md#roslyn-analyzers-task).

## <a name="tslint-task"></a>TSLint-Task

Weitere Informationen zu TSLint finden Sie im [TSLint GitHub-Repository](https://github.com/palantir/tslint).

>[!NOTE] 
>Wie Sie vielleicht wissen, ist auf der Homepage des [TSLint GitHub-Repositorys](https://github.com/palantir/tslint) angegeben, dass TSLint im Laufe des Jahres 2019 als veraltet eingestuft wird. Microsoft prüft, ob [ESLint](https://github.com/eslint/eslint) als alternativer Task infrage kommt.

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für TSLint](yaml-configuration.md#tslint-task).

## <a name="publish-security-analysis-logs-task"></a>Task zum Veröffentlichen von Sicherheitsanalyseprotokollen

Details zur Taskkonfiguration finden Sie im folgenden Screenshot und in der Liste.

![Konfigurieren des Buildtasks zum Veröffentlichen von Sicherheitsanalyseprotokollen](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artifact Name** (Artefaktname): Eine beliebige Zeichenfolgen-ID.
- **Artifact Type** (Artefakttyp): Je nach Auswahl können Sie Protokolle auf Ihrer Azure DevOps Server-Instanz oder in einer freigegebenen Datei veröffentlichen, die für den Build-Agent zugänglich ist.
- **Tools:** Sie können auswählen, ob Sie Protokolle für spezifische Tools beibehalten möchten, oder **All Tools** (Alle Tools) auswählen, um alle Protokolle beizubehalten.

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen zum Veröffentlichen von Sicherheitsprotokollen](yaml-configuration.md#publish-security-analysis-logs-task).

## <a name="security-report-task"></a>Sicherheitsberichts-Task

Details zur Konfiguration von Sicherheitsberichten finden Sie im folgenden Screenshot und in der Liste.

![Konfigurieren des Sicherheitsberichts-Buildtasks](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Reports** (Berichte): Wählen Sie das Format **Pipeline Console** (Pipelinekonsole), **TSV File** (TSV-Datei) oder **Html File** (HTML-Datei) aus. Für jedes ausgewählte Format wird eine Berichtsdatei erstellt.
- **Tools:** Wählen Sie die Tools in der Builddefinition aus, für die eine Zusammenfassung der erkannten Probleme angezeigt werden soll. Für jedes ausgewählte Tool können Sie u. U. auswählen, ob im Zusammenfassungsbericht nur Fehler oder sowohl Fehler als auch Warnungen angezeigt werden sollen.
- **Advanced Options** (Erweiterte Optionen): Wenn für eines der ausgewählten Tools keine Protokolle vorhanden sind, können Sie auswählen, ob eine Warnung oder ein Fehler protokolliert werden soll. Wenn Sie einen Fehler protokollieren, verursacht der Task einen Fehler.
- **Base Logs Folder** (Basisprotokollordner): Sie können den Ordner für Basisprotokolle anpassen, in dem die Protokolle gespeichert werden. Diese Option wird jedoch in der Regel nicht verwendet.

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für Sicherheitsberichte](yaml-configuration.md#security-report-task).

## <a name="post-analysis-task"></a>Nachanalyse-Task

Details zur Taskkonfiguration finden Sie im folgenden Screenshot und in der Liste.

![Konfigurieren des Nachanalyse-Buildtasks](./media/security-tools/a-post-analysis600.png)

- **Tools:** Wählen Sie die Tools in der Builddefinition aus, für die Sie bedingt eine Buildunterbrechung einfügen möchten. Für jedes ausgewählte Tool können Sie u. U. auswählen, ob nur bei Fehlern oder sowohl bei Fehlern als auch bei Warnungen eine Unterbrechung auftreten soll.
- **Report** (Bericht): Die Ergebnisse, die die Buildunterbrechung verursachen, können optional ausgegeben werden. Die Ergebnisse werden in das Azure DevOps-Konsolenfenster und in die Protokolldatei geschrieben.
- **Advanced Options** (Erweiterte Optionen): Wenn für eines der ausgewählten Tools keine Protokolle vorhanden sind, können Sie auswählen, ob eine Warnung oder ein Fehler protokolliert werden soll. Wenn Sie einen Fehler protokollieren, verursacht der Task einen Fehler.

Weitere Informationen zur YAML-Konfiguration für diese Aufgabe finden Sie in den [YAML-Optionen für die Nachanalyse](yaml-configuration.md#post-analysis-task).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur YAML-basierten Konfiguration finden Sie im [YAML-Konfigurationshandbuch](yaml-configuration.md).

Wenn Sie weitere Fragen zur Erweiterung „Sicherheitscodeanalyse“ und zu den angebotenen Tools haben, lesen Sie unsere [Seite mit FAQs](security-code-analysis-faq.md).