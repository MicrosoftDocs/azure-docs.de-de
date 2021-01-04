---
title: 'Quellcodeverwaltung und Entwicklungsbranches: LUIS'
description: Erfahren Sie, wie Sie Ihre Language Understanding-App (LUIS) mit einer Quellcodeverwaltung verwalten. Erfahren Sie außerdem, wie Sie Updates auf eine LUIS-App anwenden, während Sie in einem Entwicklungsbranch arbeiten.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 1f5c0c7a877964eeb480fa958c7e76eb5706122f
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561272"
---
# <a name="devops-practices-for-luis"></a>DevOps-Methoden für LUIS

Softwareentwickler, die eine Language Understanding-App (LUIS) entwickeln, können DevOps-Verfahren für [Quellcodeverwaltung](luis-concept-devops-sourcecontrol.md), [automatisierte Builds](luis-concept-devops-automation.md), [Tests](luis-concept-devops-testing.md) und [Releaseverwaltung](luis-concept-devops-automation.md#release-management) anwenden, indem sie diese Richtlinien befolgen.

## <a name="source-control-and-branch-strategies-for-luis"></a>Quellcodeverwaltungs- und Branchstrategien für LUIS

Einer der wichtigsten Faktoren für den Erfolg von DevOps ist die [Quellcodeverwaltung](/azure/devops/user-guide/source-control?view=azure-devops). Ein Quellcodeverwaltungssystem ermöglicht Entwicklern die Zusammenarbeit an Code und das Nachverfolgen von Änderungen. Durch die Verwendung von Branches können Entwickler zwischen verschiedenen Versionen der Codebasis wechseln und unabhängig von anderen Mitgliedern des Teams arbeiten. Wenn Entwickler einen [Pull Request](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) auslösen, um Updates aus einem Branch für andere Branches vorzuschlagen, oder wenn Änderungen zusammengeführt werden, können diese Vorgänge [automatisierte Builds](luis-concept-devops-automation.md) auslösen, um Code zu erstellen und fortlaufend zu testen.

Mithilfe der in diesem Dokument beschriebenen Konzepte und Anleitungen können Sie eine LUIS-App entwickeln, gleichzeitig Änderungen in einem Quellcodeverwaltungssystem nachverfolgen und dabei die folgenden bewährten Methoden für die Softwareentwicklung befolgen:

- **Quellcodeverwaltung**
  - Der Quellcode für Ihre LUIS-App liegt in einem für Menschen lesbaren Format vor.
  - Das Modell kann auf wiederholbare Weise aus der Quelle erstellt werden.
  - Der Quellcode kann mit einem Quellcoderepository verwaltet werden.
  - Anmeldeinformationen und Geheimnisse (z. B. Erstellungs- und Abonnementschlüssel) werden niemals im Quellcode gespeichert.

- **Branchen und Zusammenführen**
  - Entwickler können in unabhängigen Branches arbeiten.
  - Sie können gleichzeitig in mehreren Branches arbeiten.
  - Es ist möglich, Änderungen an einer LUIS-App von einem Branch in einen anderen zu integrieren, indem Sie ein Rebase oder Merge ausführen.
  - Entwickler können einen PR mit dem übergeordneten Branch zusammenführen.

- **Versionsverwaltung**
  - Jede Komponente in einer großen Anwendung sollte unabhängig versioniert werden, sodass Entwickler Breaking Changes oder Updates erkennen können, indem sie sich einfach die Versionsnummer ansehen.

- **Code Reviews**
  - Die Änderungen im PR werden als für Menschen lesbarer Quellcode dargestellt, der vor der Annahme des PR geprüft werden kann.

## <a name="source-control"></a>Quellcodeverwaltung

Um die [App-Schemadefinition](./app-schema-definition.md) einer LUIS-App in einem Quellcodeverwaltungssystem zu verwalten, verwenden Sie die Darstellung der App im [LUDown-Format (`.lu`)](/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0). Das `.lu`-Format ist dem `.json`-Format vorzuziehen, da es für Menschen lesbar ist, sodass das Erstellen und Überprüfen von Änderungen in PRs vereinfacht wird.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Speichern einer LUIS-App im LUDown-Format

So speichern Sie eine LUIS-App im `.lu`-Format und platzieren sie in der Quellcodeverwaltung

- ENTWEDER: [Exportieren Sie die App-Version](./luis-how-to-manage-versions.md#other-actions) als `.lu` im [LUIS-Portal](https://www.luis.ai/), und fügen Sie sie Ihrem Quellcodeverwaltungsrepository hinzu.

- ODER: Erstellen Sie mit einem Text-Editor eine `.lu`-Datei für eine LUIS-App, und fügen Sie sie Ihrem Quellcodeverwaltungsrepository hinzu.

> [!TIP]
> Wenn Sie den JSON-Export einer LUIS-App verwenden, können Sie [diesen in LUDown konvertieren](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert).  Mithilfe der Option `--sort` stellen Sie sicher, dass Absichten und Äußerungen alphabetisch sortiert werden.  
> Beachten Sie, dass die **.lu**-Exportfunktion, die im LUIS-Portal integriert ist, die Ausgabe bereits sortiert.

### <a name="build-the-luis-app-from-source"></a>Erstellen der LUIS-App aus der Quelle

Das Erstellen einer LUIS-App *aus der Quelle* bedeutet das [Erstellen einer neuen Version der LUIS-App durch Importieren der `.lu`-Quelle](./luis-how-to-manage-versions.md#import-version), das [Trainieren dieser Version](./luis-how-to-train.md) und das [Veröffentlichen](./luis-how-to-publish-app.md). Sie können diese Schritte im LUIS-Portal oder über die Befehlszeile ausführen:

- Verwenden Sie das LUIS-Portal, um [die `.lu`-Version der App aus der Quellcodeverwaltung zu importieren](./luis-how-to-manage-versions.md#import-version) und die App zu [trainieren](./luis-how-to-train.md) und zu [veröffentlichen](./luis-how-to-publish-app.md).

- Verwenden Sie die [Befehlszeilenschnittstelle von Bot Framework für LUIS](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) über die Befehlszeile oder in einem CI/CD-Workflow, um die `.lu`-Version der App aus der Quellcodeverwaltung in eine LUIS-Anwendung zu [importieren](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) und diese App zu [trainieren](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) und zu [veröffentlichen](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish).

### <a name="files-to-maintain-under-source-control"></a>Dateien für die Quellcodeverwaltung

Die folgenden Dateitypen für Ihre LUIS-Anwendung sollten mit einer Quellcodeverwaltung verwaltet werden:

- `.lu`-Datei für die LUIS-Anwendung

- [Definitionsdateien für Komponententests](luis-concept-devops-testing.md#writing-tests) (Äußerungen und erwartete Ergebnisse)

- [Batchtestdateien](./luis-concept-batch-test.md#batch-file-format) (Äußerungen und erwartete Ergebnisse) für Leistungstests

### <a name="credentials-and-keys-are-not-checked-in"></a>Ausschließen von Anmeldeinformationen und Schlüsseln

Schließen Sie keine Abonnementschlüssel oder ähnliche vertrauliche Werte in die Dateien ein, die Sie in Ihr Repository einchecken, da sie dort möglicherweise für nicht autorisierte Mitarbeiter einsehbar sind. Zu den Schlüsseln und anderen Werten, die Sie nicht einchecken sollten, zählen:

- LUIS-Schlüssel für Erstellung und Vorhersage
- LUIS-Endpunkte für Erstellung und Vorhersage
- Azure-Abonnementschlüssel
- Zugriffstoken, z. B. das Token für einen Azure-[Dienstprinzipal](/cli/azure/ad/sp?view=azure-cli-latest) für die Automation-Authentifizierung

#### <a name="strategies-for-securely-managing-secrets"></a>Strategien für die sichere Verwaltung von Geheimnissen

Zu den Strategien für die sichere Verwaltung von Geheimnissen gehören:

- Wenn Sie die Git-Versionskontrolle verwenden, können Sie Laufzeitgeheimnisse in einer lokalen Datei speichern und das Einchecken der Datei verhindern, indem Sie in einer [.gitignore](https://git-scm.com/docs/gitignore)-Datei ein Muster hinzufügen, das dem Dateinamen entspricht.
- Bei einem Automatisierungsworkflow können Sie Geheimnisse sicher in der von der Automatisierungstechnologie bereitgestellten Parameterkonfiguration speichern. Wenn Sie z. B. [GitHub Actions](https://github.com/features/actions) verwenden, können Sie Geheimnisse sicher in [GitHub-Geheimnissen](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) speichern.

## <a name="branching-and-merging"></a>Branchen und Zusammenführen

Verteilte Versionskontrollsysteme wie Git bieten viel Flexibilität bei der Veröffentlichung, Freigabe, Überprüfung und Iteration von Codeänderungen durch die Teammitglieder über Entwicklungsbranches, die für andere Benutzer freigegeben werden. Nutzen Sie eine [Git-Branchstrategie](/azure/devops/repos/git/git-branching-guidance), die für Ihr Team geeignet ist.

Unabhängig von Ihrer Verzweigungsstrategie besteht das Hauptprinzip darin, dass die Teammitglieder an der Lösung innerhalb eines *Featurebranches* arbeiten können – und zwar unabhängig von der Arbeit, die in anderen Branches ausgeführt wird.

So unterstützen Sie das unabhängige Arbeiten in Branches bei einem LUIS-Projekt

- **Der Mainbranch verfügt über eine eigene LUIS-App:** Diese App stellt den aktuellen Zustand der Projektmappe für Ihr Projekt dar. Die aktuell aktive Version sollte dabei immer der `.lu`-Quelle im Mainbranch zugeordnet sein. Alle Updates an der `.lu`-Quelle für diese App sollten überprüft und getestet werden, damit diese App jederzeit in Buildumgebungen (z. B. in der Produktion) bereitgestellt werden kann. Wenn Updates an der `.lu`-Quelle aus einem Featurebranch im Mainbranch zusammengeführt werden, sollten Sie eine neue Version in der LUIS-App erstellen und [die Versionsnummer aktualisieren](#versioning).

- **Jeder Featurebranch muss eine eigene Instanz einer LUIS-App verwenden**. Die Entwickler arbeiten mit dieser App in einem Featurebranch ohne Auswirkung auf Entwickler, die in anderen Branches arbeiten. Eine App in einem solchen Entwicklungsbranch ist eine Arbeitskopie, die gelöscht werden sollte, wenn der Featurebranch gelöscht wird.

![Git-Featurebranch](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Arbeiten in unabhängigen Branches für die Entwickler

Entwickler können wie folgt unabhängig von anderen Branches an Updates für eine LUIS-App arbeiten:

1. Erstellen Sie einen Featurebranch aus dem Mainbranch (abhängig von der Branchstrategie, in der Regel Main- oder Entwicklungsbranch).

1. [Erstellen Sie eine neue LUIS-App im LUIS-Portal](./luis-how-to-start-new-app.md) (die *„Entwicklungsbranch-App“* ), die ausschließlich der Unterstützung der Arbeit in diesem Featurebranch dient.

   * Wenn die `.lu`-Quelle für die Projektmappe bereits in Ihrem Branch vorhanden ist, da sie nach der Arbeit in einem anderen Branch zuvor im Projekt gespeichert wurde, erstellen Sie Ihren eigenen Entwicklungsbranch der LUIS-App, indem Sie die `.lu`-Datei importieren.

   * Wenn Sie mit der Arbeit an einem neuen Projekt beginnen, verfügen Sie noch nicht über die `.lu`-Quelle für den Mainbranch der LUIS-App im Repository. Sie erstellen die `.lu`-Datei, indem Sie Ihre Entwicklungsbranch-App im Portal exportieren, nachdem Sie die Arbeit am Featurebranch abgeschlossen haben, und sie als Teil Ihres PR einreichen.

1. Arbeiten Sie an der aktiven Version Ihrer Entwicklungsbranch-App, um die erforderlichen Änderungen zu implementieren. Es wird empfohlen, sämtliche Arbeiten am Featurebranch in einer einzigen Version Ihrer Entwicklungsbranch-App auszuführen. Wenn Sie in Ihrer Entwicklungsbranch-App mehrere Version erstellen, achten Sie beim Auslösen Ihres PR genau darauf, welche Version die Änderungen enthält, die Sie einchecken möchten.

1. Testen Sie die Updates. Ausführliche Informationen zum Testen Ihrer Entwicklungsbranch-App finden Sie unter [Testen für LUIS DevOps](luis-concept-devops-testing.md).

1. Exportieren Sie die aktive Version der Entwicklungsbranch-App als `.lu` aus der [Versionsliste](./luis-how-to-manage-versions.md).

1. Checken Sie Ihre Updates ein, und laden Sie zum Peer Review Ihrer Updates ein. Wenn Sie GitHub verwenden, lösen Sie einen [Pull Request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) aus.

1. Wenn die Änderungen genehmigt werden, führen Sie die Updates im Mainbranch zusammen. An diesem Punkt erstellen Sie eine neue [Version](./luis-how-to-manage-versions.md) des *Mainbranch* der LUIS-App, für die Sie die aktualisierte `.lu`-Quelle im Mainbranch verwenden. Überlegungen zum Festlegen von Versionsnamen finden Sie unter [Versionsverwaltung](#versioning).

1. Wenn der Featurebranch gelöscht wird, empfiehlt es sich, auch den Entwicklungsbranch der LUIS-App zu löschen, den Sie für die Arbeit am Featurebranch erstellt haben.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Gleichzeitiges Arbeiten an mehreren Branches durch Entwickler

Wenn Sie das oben unter [Arbeiten in unabhängigen Branches für die Entwickler](#developers-can-work-from-independent-branches) beschriebene Muster befolgen, verwenden Sie in jedem Featurebranch eine eigene LUIS-Anwendung. Ein einzelner Entwickler kann gleichzeitig an mehreren Branches arbeiten, sofern er zum richtigen Entwicklungsbranch der LUIS-App wechselt, an der er gerade arbeitet.

Es wird empfohlen, für den Featurebranch und den Entwicklungsbranch der LUIS-App, den Sie für die Arbeit am Featurebranch erstellen, denselben Namen zu verwenden, damit Sie nicht versehentlich an der falschen App arbeiten.

Wie bereits erwähnt, wird der Einfachheit halber empfohlen, in jedem Entwicklungsbranch der App in einer einzigen Version zu arbeiten. Wenn Sie mehrere Versionen verwenden, achten Sie darauf, dass Sie die richtige Version aktivieren, wenn Sie zwischen Entwicklungsbranch-Apps wechseln.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Gleichzeitiges Arbeiten mehrerer Entwickler am selben Branch

Sie können es mehreren Entwicklern ermöglichen, gleichzeitig am selben Featurebranch zu arbeiten:

- Die Entwickler checken denselben Featurebranch aus und pushen bzw. pullen Änderungen, die sie selbst oder andere Entwickler vorgenommen haben, während sie wie gewohnt weiterarbeiten.

- Wenn Sie das oben unter [Arbeiten in unabhängigen Branches für die Entwickler](#developers-can-work-from-independent-branches) beschriebene Muster befolgen, verwendet dieser Branch eine eigene LUIS-Anwendung für die Entwicklung. Dieser Entwicklungsbranch der LUIS-App wird vom ersten Mitglied des Entwicklungsteams erstellt, das mit der Arbeit in diesem Featurebranch beginnt.

- Fügen Sie dem Entwicklungsbranch der LUIS-App [Teammitglieder als Mitwirkende](./luis-how-to-collaborate.md) hinzu.

- Wenn die Arbeit am Featurebranch abgeschlossen ist, exportieren Sie die aktive Version der LUIS-App im Entwicklungsbranch als `.lu` aus der [Versionsliste](./luis-how-to-manage-versions.md), speichern die aktualisierte `.lu`-Datei im Repository, checken die Änderungen ein und führen einen Pull Request aus.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Integrieren von Änderungen aus einem Branch in einen anderen per Rebase oder Merge

Andere Entwickler in Ihrem Team, die in einem anderen Branch arbeiten, haben möglicherweise Updates an der `.lu`-Quelle vorgenommen und mit dem Mainbranch zusammengeführt, nachdem Sie Ihren Featurebranch erstellt hatten. Möglicherweise möchten Sie deren Änderungen in Ihre Arbeitsversion einbinden, bevor Sie weitere eigene Änderungen an Ihrem Featurebranch vornehmen. Dazu können Sie ein [Rebase oder Merge im Mainbranch ausführen](https://git-scm.com/book/en/v2/Git-Branching-Rebasing), genau wie bei anderen Coderessourcen. Da die LUIS-App im LUDown-Format für Menschen lesbar ist, unterstützt sie die Zusammenführung mit Standardmergetools.

Wenn Sie ein Rebase für Ihre LUIS-App in einem Featurebranch ausführen, befolgen Sie diese Tipps:

- Vergewissern Sie sich vor dem Rebase oder Merge, dass Ihre lokale Kopie der `.lu`-Quelle für Ihre App über alle aktuellen Änderungen verfügt, die Sie im LUIS-Portal vorgenommen haben. Exportieren Sie Ihre App dazu zunächst erneut aus dem Portal. Auf diese Weise können Sie sicherstellen, dass keine Änderungen verloren gehen, die Sie im Portal vorgenommen und noch nicht exportiert haben.

- Verwenden Sie während des Zusammenführens Standardtools, um Mergekonflikte zu beheben.

- Vergessen Sie nicht, Ihre App nach Abschluss des Rebase- oder Mergevorgangs erneut im Portal zu importieren, damit Sie mit der aktualisierten App arbeiten, während Sie weiterhin eigene Änderungen anwenden.

### <a name="merge-prs"></a>Zusammenführen von Pull Requests

Nachdem Ihr Pull Request (PR) genehmigt wurde, können Sie Ihre Änderungen mit dem Mainbranch zusammenführen. Für die LUDown-Quelle einer LUIS-App gelten keine besonderen Überlegungen: Sie ist für Menschen lesbar und unterstützt damit das Zusammenführen mit Standardmergetools. Sämtliche Mergekonflikte können auf die gleiche Weise wie bei anderen Quelldateien behandelt werden.

Nachdem Ihr PR zusammengeführt wurde, empfiehlt es sich, eine Bereinigung durchzuführen:

- Löschen Sie den Branch in Ihrem Repository.

- Löschen Sie den Entwicklungsbranch der LUIS-App, den Sie für den Featurebranch erstellt haben.

Sie sollten wie auch bei anderen Anwendungscoderessourcen Komponententests für LUIS-App-Updates schreiben. Verwenden Sie Continuous Integration-Workflows, um Folgendes zu testen:

- Updates in einem PR – bevor der PR zusammengeführt wird
- Der Mainbranch der LUIS-App wurde nach einem PR genehmigt, und die Änderungen wurden im Mainbranch zusammengeführt.

Weitere Informationen finden Sie unter [Testen für LUIS DevOps](luis-concept-devops-testing.md). Weitere Informationen zum Implementieren von Workflows finden Sie unter [Automatisierungsworkflows für LUIS DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Code Reviews

Eine LUIS-App im LUDown-Format ist für Menschen lesbar. Damit unterstützt sie die Kommunikation von Änderungen in einem PR für die Überprüfung. Komponententestdateien werden ebenfalls im LUDown-Format geschrieben und können daher ganz einfach bei einem PR überprüft werden.

## <a name="versioning"></a>Versionsverwaltung

Eine Anwendung besteht aus mehreren Komponenten. Dies kann z. B. ein Bot sein, der in [Azure Bot Service](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA Maker](https://www.qnamaker.ai/), [Azure Speech Service](../speech-service/overview.md) usw. ausgeführt wird. Um das Ziel von lose gekoppelten Anwendungen zu erreichen, verwenden Sie eine [Versionskontrolle](/azure/devops/learn/git/what-is-version-control), damit jede Komponente einer Anwendung unabhängig versioniert wird. Auf diese Weise können Entwickler Breaking Changes oder Updates erkennen, indem sie einfach die Versionsnummer überprüfen. Es ist einfacher, die LUIS-App unabhängig von anderen Komponenten zu versionieren, wenn Sie sie in einem eigenen Repository verwalten.

Auf die LUIS-App im Mainbranch sollte ein Versionsverwaltungsschema angewendet werden. Wenn Sie Updates an der `.lu`-Datei für eine LUIS-App im Mainbranch zusammenführen, importieren Sie dann diese aktualisierte Quelle in eine neue Version der LUIS-App für den Mainbranch.

Es wird die Verwendung eines numerischen Versionsverwaltungsschemas für die Hauptversion der LUIS-App empfohlen, zum Beispiel:

`major.minor[.build[.revision]]`

Bei jedem Update wird die letzte Ziffer der Versionsnummer erhöht.

Über die Haupt-/Nebenversion kann der Umfang der Änderungen an der Funktionalität der LUIS-App angegeben werden:

* Hauptversion: Eine bedeutende Änderung, z. B. Unterstützung einer neuen [Absicht](./luis-concept-intent.md) oder [Entität](./luis-concept-entity-types.md)
* Nebenversion: Eine kleinere, abwärtskompatible Änderung, z. B. nach einem bedeutenden neuen Training
* Build: Keine Funktionsänderung, sondern nur ein anderer Build

Nachdem Sie die Versionsnummer für die aktuelle Revision des Mainbranch Ihrer LUIS-App festgelegt haben, müssen Sie die neue App-Version erstellen, testen und an einem Endpunkt veröffentlichen, von dem aus sie in unterschiedlichen Buildumgebungen verwendet werden kann, z. B. bei der Qualitätssicherung oder in der Produktion. Es wird dringend empfohlen, alle diese Schritte in einem Continuous Integration-Workflow (CI) zu automatisieren.

Siehe:
- Unter den [Automatisierungsworkflows](luis-concept-devops-automation.md) finden Sie ausführliche Informationen zum Implementieren eines CI-Workflows zum Testen und Veröffentlichen einer LUIS-App.
- Unter [Releaseverwaltung](luis-concept-devops-automation.md#release-management) finden Sie Informationen zum Bereitstellen Ihrer LUIS-App.

### <a name="versioning-the-feature-branch-luis-app"></a>Versionsverwaltung der LUIS-App im Featurebranch

Wenn Sie in einem Entwicklungsbranch der LUIS-App arbeiten, den Sie erstellt haben, um die Arbeit in einem Featurebranch zu unterstützen, exportieren Sie Ihre App nach Abschluss Ihrer Arbeit und fügen die aktualisierte `'lu`-Datei in Ihren PR ein. Der Branch in Ihrem Repository und der Entwicklungsbranch der LUIS-App sollten gelöscht werden, nachdem der PR mit dem Mainbranch zusammengeführt wurde. Da diese App ausschließlich zur Unterstützung der Arbeit im Featurebranch dient, gibt es kein spezifisches Versionsverwaltungsschema, das Sie auf diese App anwenden müssen.

Erst wenn die Änderungen in Ihrem PR mit dem Mainbranch zusammengeführt werden, sollte die Versionsverwaltung angewandt werden, damit alle Updates am Mainbranch unabhängig voneinander versioniert werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Testen für LUIS DevOps](luis-concept-devops-testing.md).
* Erfahren Sie, wie Sie [DevOps für LUIS mit GitHub implementieren](luis-how-to-devops-with-github.md).