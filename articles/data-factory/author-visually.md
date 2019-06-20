---
title: Visuelles Erstellen in Azure Data Factory | Microsoft-Dokumentation
description: Informationen zum Verwenden der visuellen Erstellung in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: b32e4abebdfb93312c60a25ca8b483f071e5e73c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507828"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuelles Erstellen in Azure Data Factory
Mithilfe der Azure Data Factory-Benutzeroberfläche (UX) können Sie Ressourcen für Ihre Data Factory visuell erstellen und bereitstellen, ohne Code schreiben zu müssen. Sie können Aktivitäten auf eine Pipelinecanvas ziehen, Testläufe ausführen, iterativ debuggen sowie Ihre Pipelineausführungen bereitstellen und überwachen. Die Benutzeroberfläche kann auf zwei Arten zum visuellen Erstellen verwendet werden:

- Direktes Erstellen mit dem Data Factory-Dienst
- Erstellen mit der Azure Repos Git-Integration für die Kollaboration, Quellcodeverwaltung und Versionsverwaltung

## <a name="author-directly-with-the-data-factory-service"></a>Direktes Erstellen mit dem Data Factory-Dienst
Das visuelle Erstellen mit dem Data Factory-Dienst unterscheidet sich in zwei Punkten vom visuellen Erstellen mit der Git-Integration:

- Der Data Factory-Dienst umfasst kein Repository zum Speichern der JSON-Entitäten für Ihre Änderungen.
- Der Data Factory-Dienst ist nicht für Zusammenarbeit oder Versionskontrolle optimiert.

![Konfigurieren des Data Factory-Diensts](media/author-visually/configure-data-factory.png)

Wenn Sie die **UX-Canvas** für die Erstellung zum direkten Erstellen mit dem Data Factory-Dienst verwenden, ist nur der Modus **Alle veröffentlichen** verfügbar. Alle vorgenommenen Änderungen werden direkt im Data Factory-Dienst veröffentlicht.

![Modus „Veröffentlichen“](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Erstellen mit der Azure Repos Git-Integration
Das visuelle Erstellen mit der Azure Repos Git-Integration unterstützt die Quellcodeverwaltung und Kollaboration beim Arbeiten an Data Factory-Pipelines. Sie können eine Data Factory einem Azure Repos Git-Organisationsrepository u.a. für die Quellcodeverwaltung, Kollaboration, Versionsverwaltung zuordnen. Eine einzelne Azure Repos Git-Organisation kann über mehrere Repositorys verfügen. Allerdings kann ein Azure Repos Git-Repository nur einer einzigen Data Factory zugeordnet werden. Wenn Sie noch nicht über eine Azure Repos-Organisation oder ein -Repository verfügen, folgen Sie [diesen Anweisungen](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) zum Erstellen der Ressourcen.

> [!NOTE]
> Sie können Skript- und Datendateien in einem Azure Repos Git-Repository speichern. Allerdings müssen Sie die Dateien manuell in Azure Storage hochladen. Eine Data Factory-Pipeline lädt Skript- oder Datendateien, die in einem Azure Repos Git-Repository gespeichert sind, nicht automatisch in Azure Storage hoch.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurieren eines Azure Repos Git-Repositorys mit Azure Data Factory
Es gibt zwei Methoden zum Konfigurieren eines Azure Repos Git-Repositorys mit einer Data Factory.

#### <a name="method1"></a> Konfigurationsmethode 1 (Azure Repos-Git-Repository): Seite „Erste Schritte“

Wechseln Sie in Azure Data Factory zur Seite **Erste Schritte**. Wählen Sie **Coderepository konfigurieren** aus:

![Konfigurieren eines Coderepositorys für Azure Repos](media/author-visually/configure-repo.png)

Der Konfigurationsbereich **Repositoryeinstellungen** wird angezeigt:

![Konfigurieren der Repositoryeinstellungen](media/author-visually/repo-settings.png)

In dem Bereich werden die folgenden Einstellungen für das Coderepository für Azure Repos angezeigt:

| Einstellung | BESCHREIBUNG | Wert |
|:--- |:--- |:--- |
| **Repositorytyp** | Der Typ des Coderepositorys für Azure Repos.<br/> | Azure Repos Git |
| **Azure Active Directory** | Ihr Name des Azure AD-Mandanten. | `<your tenant name>` |
| **Azure Repos-Organisation** | Der Name Ihrer Azure Repos-Organisation. Sie können den Namen Ihrer Azure Repos-Organisation unter `https://{organization name}.visualstudio.com` finden. Sie können sich [bei Ihrer Azure Repos-Organisation anmelden](https://www.visualstudio.com/team-services/git/), um auf Ihr Visual Studio-Profil zuzugreifen und Ihre Repositorys und Projekte anzuzeigen. | `<your organization name>` |
| **Projektname** | Der Name Ihres Azure Repos-Projekts. Sie können den Namen Ihres Azure Repos-Projekts unter `https://{organization name}.visualstudio.com/{project name}` finden. | `<your Azure Repos project name>` |
| **Repositoryname** | Der Name Ihres Azure Repos-Coderepositorys. Azure Repos-Projekte enthalten Git-Repositorys zum Verwalten Ihres Quellcodes, wenn Ihr Projekt umfangreicher wird. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Projekt befindet. | `<your Azure Repos code repository name>` |
| **Collaboration branch** (Kollaborationsbranch) | Ihr Branch für die Azure Repos-Kollaboration, der für die Veröffentlichung verwendet wird. Standardmäßig ist dies `master`. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten. | `<your collaboration branch name>` |
| **Stammordner** | Ihr Stammordner im Branch für die Azure Repos-Kollaboration. | `<your root folder name>` |
| **Vorhandene Data Factory-Ressourcen in Repository importieren** | Gibt an, ob vorhandene Data Factory-Ressourcen aus dem **Dokumenterstellungsbereich** in ein Azure Repos Git-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Data Factory-Ressourcen in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln (d. h. die verknüpften Dienste und Datasets werden in separate JSONs exportiert). Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Konfigurationsmethode 2 (Azure Repos-Git-Repository): UX-Canvas für die Erstellung
Suchen Sie in Azure Data Factory in der **UX-Canvas** für die Erstellung nach Ihrer Data Factory. Klicken Sie auf das Dropdownmenü **Data Factory**, und wählen Sie dann **Coderepository konfigurieren** aus.

Ein Konfigurationsbereich wird angezeigt. Ausführliche Informationen zu den Konfigurationseinstellungen finden Sie in den Beschreibungen zu <a href="#method1">Konfigurationsmethode 1</a>.

![Konfigurieren der Repositoryeinstellungen für Erstellung in der UX-Canvas](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Verwenden eines anderen Azure Active Directory-Mandanten

Sie können ein Azure Repos Git-Repository in einem anderen Azure Active Directory-Mandanten erstellen. Zum Angeben eines anderen Azure AD-Mandanten müssen Sie über Administratorberechtigungen für das verwendete Azure-Abonnement verfügen.

### <a name="use-your-personal-microsoft-account"></a>Verwenden Ihres persönlichen Microsoft-Kontos

Um ein persönliches Microsoft-Konto für die Git-Integration zu verwenden, können Sie Ihr persönliches Azure-Repository mit dem Active Directory Ihrer Organisation verknüpfen.

1. Fügen Sie Ihr persönliches Microsoft-Konto als Gast zum Active Directory Ihrer Organisation hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/b2b/add-users-administrator.md).

2. Melden Sie sich mit Ihrem persönlichen Microsoft-Konto beim Azure-Portal an. Wechseln Sie dann zum Active Directory Ihrer Organisation.

3. Wechseln Sie zum Abschnitt für Azure DevOps – dort wird jetzt Ihr persönliches Repository angezeigt. Wählen Sie das Repository aus, und verbinden Sie es mit Active Directory.

Nach diesen Konfigurationsschritten ist Ihr persönliches Repository verfügbar, wenn Sie die Git-Integration auf der Data Factory-Benutzeroberfläche einrichten.

Weitere Informationen zum Verbinden von Azure Repos mit dem Active Directory Ihrer Organisation finden Sie unter [Verbinden Ihrer Azure DevOps-Organisation mit Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

### <a name="switch-to-a-different-git-repo"></a>Wechseln zu einem anderen Git-Repository

Um zu einem anderen Git-Repository zu wechseln, verwenden Sie das Symbol oben rechts auf der Data Factory-Übersichtseite, wie im folgenden Screenshot gezeigt. Wenn das Symbol nicht angezeigt wird, löschen Sie den lokalen Browsercache. Wählen Sie das Symbol aus, um die Zuordnung zum aktuellen Repository zu entfernen.

Nachdem Sie die Zuordnung zum aktuellen Repository entfernt haben, können Sie Ihre Git-Einstellungen zur Verwendung eines anderen Repositorys konfigurieren. Dann können Sie vorhandene Data Factory-Ressourcen in das neue Repository importieren.

![Entfernen der Zuordnung zum aktuellen Git-Repository](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Verwenden von Versionskontrolle
Versionskontrollsysteme (auch als _Quellcodeverwaltung_ bezeichnet) ermöglichen Entwicklern die Zusammenarbeit an Code und das Nachverfolgen von an der Codebasis vorgenommenen Änderungen. Quellcodeverwaltung ist ein unverzichtbares Tool für Projekte, an denen mehrere Entwickler arbeiten.

Jedes Azure Repos Git-Repository, das einer Data Factory zugeordnet ist, verfügt über einen Branch für die Kollaboration. (`master` ist der Standardbranch für die Kollaboration.) Benutzer können auch Featurebranches verwenden, indem sie auf **+ Neuer Branch** klicken und in den Featurebranches Entwicklungsschritte ausführen.

![Ändern des Codes durch Synchronisieren oder Veröffentlichen](media/author-visually/sync-publish.png)

Wenn Sie mit der Featurebereitstellung in Ihrem Featurebranch fertig sind, können Sie auf **Pull Request erstellen** klicken. Dann gelangen Sie zu Azure Repos Git und können Pull Requests auslösen, Codereviews durchführen und Änderungen an Ihrem Kollaborationsbranch zusammenführen. (`master` ist die Standardeinstellung.) Sie können nur Veröffentlichungen für den Data Factory-Dienst Ihres Kollaborationsbranchs durchführen. 

![Erstellen eines neuen Pull Requests](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurieren von Veröffentlichungseinstellungen

Fügen Sie dem Stammordner im Kollaborationsbranch eine `publish_config.json`-Datei hinzu, um den Branch für die Veröffentlichung zu konfigurieren (der Branch, in dem Resource Manager-Vorlage gespeichert werden). Die Data Factory liest die Datei, sucht das Feld `publishBranch` und erstellt wenn nötig einen neuen Branch, mit dem angegebenen Wert. Anschließend speichert sie die Resource Manager-Vorlagen am angegebenen Ort. Beispiel:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Wenn Sie die Veröffentlichung im Git-Modus durchführen, können Sie überprüfen, ob die Data Factory wie im folgenden Screenshot dargestellt den erwarteten Branch für die Veröffentlichung verwendet:

![Überprüfen des Branch für die Veröffentlichung](media/author-visually/configure-publish-branch.png)

Wenn Sie einen neuen Branch für die Veröffentlichung angeben, löscht die Data Factory den vorherigen Branch für die Veröffentlichung nicht. Wenn Sie den vorherigen Branch für die Veröffentlichung über eine Remoteverbindung ausführen möchten, löschen Sie ihn manuell.

Die Data Factory liest die `publish_config.json`-Datei nur, wenn diese die Factory lädt. Wenn die Factory bereits im Portal geladen ist, aktualisieren Sie den Browser, damit Ihre Änderungen übernommen werden.

### <a name="publish-code-changes"></a>Veröffentlichen von Codeänderungen
Nachdem Sie die Änderungen des Kollaborationsbranchs zusammengeführt haben (`master` ist die Standardeinstellung), können Sie **Veröffentlichen** auswählen, um Ihre Codeänderungen manuell im Branch „master“ für den Data Factory-Dienst zu veröffentlichen.

![Veröffentlichen von Änderungen für den Data Factory-Dienst](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Der Masterbranch ist nicht repräsentativ für das, was im Data Factory-Dienst bereitgestellt wird. Der Masterbranch *muss* manuell im Data Factory-Dienst veröffentlicht werden.

### <a name="advantages-of-git-integration"></a>Vorteile der Git-Integration

-   **Quellcodeverwaltung.** Wenn Ihre Data Factory-Workloads sehr wichtig sind, sollten Sie Ihre Factory in Git integrieren, um verschiedene Vorteile der Quellcodeverwaltung wie die folgenden nutzen zu können:
    -   Möglichkeit zum Nachverfolgen/Überwachen von Änderungen
    -   Möglichkeit zum Rückgängigmachen von Änderungen, die Fehler verursacht haben
-   **Partielle Speicherungen.** Wenn Sie viele Änderungen an Ihrer Factory vornehmen, werden Sie feststellen, dass Sie im regulären LIVE-Modus Ihre Änderungen nicht als Entwurf speichern können, da Sie nicht bereit sind oder Änderungen nicht verlieren möchten, falls der Computer abstürzt. Mit der Git-Integration können Sie Ihre Änderungen weiterhin inkrementell speichern und erst dann in der Factory veröffentlichen, wenn Sie dazu bereit sind. Git fungiert als Stagingstelle für Ihre Arbeit, bis Sie Ihre Änderungen zu Ihrer Zufriedenheit getestet haben.
-   **Kollaboration und Steuerung.** Wenn mehrere Ihrer Teammitglieder an derselben Factory arbeiten, empfiehlt es sich, Ihre Teamkollegen über einen Code Review-Prozess miteinander zusammenarbeiten zu lassen. Sie können Ihre Factory auch so einrichten, dass nicht jeder Mitwirkende an der Factory Berechtigungen zum Bereitstellen in der Factory hat. Teammitglieder sind möglicherweise nur berechtigt, Änderungen über Git vorzunehmen, und nur bestimmte Personen im Team dürfen die Änderungen an der Factory „veröffentlichen“.
-   **Anzeigen von Unterschieden als Diff.** Im Git-Modus können Sie ein übersichtliches Diff der Nutzlast anzeigen, die in der Factory veröffentlicht werden soll. In diesem Diff sehen Sie alle Ressourcen/Entitäten, die seit der letzten Veröffentlichung in der Factory geändert/hinzugefügt/gelöscht wurden. Basierend auf diesem Diff können Sie entweder mit der Veröffentlichung fortfahren oder zurückkehren, die Änderungen überprüfen und den Vorgang später wiederaufnehmen.
-   **Bessere CI/CD.** Wenn Sie den Git-Modus verwenden, können Sie Ihre Releasepipeline so konfigurieren, dass sie automatisch ausgelöst wird, sobald Änderungen an der Entwicklungsfactory vorgenommen wurden. Sie können auch die Eigenschaften in Ihrer Factory anpassen, die als Parameter in der Resource Manager-Vorlage verfügbar sind. Es kann hilfreich sein, nur die erforderlichen Eigenschaften als Parameter beizubehalten und alles andere fest zu codieren.
-   **Höhere Leistung.** Eine durchschnittliche Factory wird im Git-Modus 10-mal schneller geladen als im normalen LIVE-Modus, da die Ressourcen über Git heruntergeladen werden.

### <a name="best-practices-for-git-integration"></a>Bewährte Methoden für die Git-Integration

-   **Berechtigungen** Normalerweise ist es nicht sinnvoll, allen Teammitgliedern Berechtigungen zum Aktualisieren der Factory zuzuweisen.
    -   Alle Teammitglieder sollten über Leseberechtigungen für die Data Factory verfügen.
    -   Nur eine ausgewählte Gruppe von Personen sollte in der Factory veröffentlichen können. Dazu müssen sie der Rolle „Data Factory-Mitwirkender“ für die Factory angehören.
    -   Eine weitere bewährte Methode der Quellcodeverwaltung besteht darin, keine direkten Check-Ins in den Kollaborationsbranch zuzulassen. Diese Anforderung verhindert Fehler, da jeder Check-In einen Pull Request-Vorgang durchläuft.
-   **Wechseln von Modi.**
    -    Wenn Sie sich im Git-Modus befinden, wird empfohlen, nicht mehrfach zwischen diesem und dem LIVE-Modus zu wechseln, vor allem, da Änderungen im LIVE-Modus beim Wechsel zurück zu Git nicht sichtbar sind. Versuchen Sie, Änderungen im Git-Modus vorzunehmen und diese dann über die Benutzeroberfläche zu veröffentlichen.
    -   Ebenso sollten Sie keine Data Factory-PowerShell-Cmdlets verwenden, da diese die gleiche Wirkung erzielen, da die bereitgestellten Änderungen direkt auf die Livefactory angewandt werden.
-   **Verwenden von Kennwörtern aus Azure Key Vault.**
    -   Es wird dringend empfohlen, Azure Key Vault zum Speichern von Verbindungszeichenfolgen oder Kennwörtern in verknüpften Data Factory-Diensten zu verwenden.
    -   Wir speichern diese geheimen Informationen nicht in Git (aus Sicherheitsgründen), sodass Änderungen an verknüpften Diensten sofort in der Livefactory veröffentlicht werden. Diese sofortige Veröffentlichung ist manchmal nicht erwünscht, da die Änderungen möglicherweise nicht getestet wurden und somit dem Sinn von Git entgegen stehen.
    -   Daher müssen alle diese Geheimnisse über Azure Key Vault von verknüpften Diensten abgerufen werden.
    -   Ein weiterer Vorteil von Key Vault ist, dass CI/CD vereinfacht wird, da Sie diese Geheimnisse nicht bei der Bereitstellung der Resource Manager-Vorlage angeben müssen.

## <a name="author-with-github-integration"></a>Erstellen der GitHub-Integration

Das visuelle Erstellen mit GitHub-Integration unterstützt Quellcodeverwaltung und Kollaboration beim Arbeiten an den Data Factory-Pipelines. Sie können eine Data Factory einem GitHub-Kontorepository für die Quellcodeverwaltung, Kollaboration und Versionsverwaltung zuordnen. Ein einzelnes GitHub-Konto kann über mehrere Repositorys verfügen. Allerdings kann ein GitHub-Repository nur einer einzigen Data Factory zugeordnet werden. Wenn Sie noch nicht über ein GitHub-Konto oder -Repository verfügen, folgen Sie  [diesen Anweisungen](https://github.com/join)  zum Erstellen der Ressourcen.

Die GitHub-Integration von Data Factory unterstützt sowohl das öffentliche GitHub (also [https://github.com](https://github.com)) als auch GitHub Enterprise. Sie können öffentliche und private GitHub-Repositorys mit Data Factory verwenden, sofern Sie über Lese- und Schreibberechtigungen für das Repository in GitHub verfügen.

Zum Konfigurieren eines GitHub-Repositorys müssen Sie über Administratorberechtigungen für das verwendete Azure-Abonnement verfügen.

Das folgende Video enthält eine neun-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Einschränkungen

- Sie können Skript- und Datendateien in einem GitHub-Repository speichern. Allerdings müssen Sie die Dateien manuell in Azure Storage hochladen. Eine Data Factory-Pipeline lädt Skript- oder Datendateien, die in einem GitHub-Repository gespeichert sind, nicht automatisch in Azure Storage hoch.

- GitHub Enterprise-Versionen vor 2.14.0 funktionieren nicht im Browser Microsoft Edge.

- Die GitHub-Integration der visuellen Data Factory-Entwicklungstools funktioniert nur in der allgemein verfügbaren Version von Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Konfigurieren eines GitHub-Repositorys mit Azure Data Factory

Es gibt zwei Methoden zum Konfigurieren eines GitHub-Repositorys mit einer Data Factory.

**Konfigurationsmethode 1 (öffentliches Repository): Seite „Erste Schritte“**

Wechseln Sie in Azure Data Factory zur Seite  **Erste Schritte** . Wählen Sie  **Coderepository konfigurieren** aus:

![Seite „Erste Schritte“ von Data Factory](media/author-visually/github-integration-image1.png)

Der Konfigurationsbereich  **Repositoryeinstellungen**  wird angezeigt:

![GitHub-Repositoryeinstellungen](media/author-visually/github-integration-image2.png)

In dem Bereich werden die folgenden Einstellungen für das Coderepository für Azure Repos angezeigt:

| **Einstellung**                                              | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Wert**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Repositorytyp**                                      | Der Typ des Coderepositorys für Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub-Konto**                                       | Ihr GitHub-Kontoname. Sie finden diesen Namen unter https:\//github.com/{Kontoname}/{Repositoryname}. Beim Navigieren zu dieser Seite werden Sie zur Eingabe der GitHub-OAuth-Anmeldeinformationen zu Ihrem GitHub-Konto aufgefordert.                                                                                                                                                                                                                                               |                    |
| **Repositoryname**                                       | Der Name Ihres GitHub-Coderepositorys. GitHub-Konten enthalten Git-Repositorys zum Verwalten Ihres Quellcodes. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Konto befindet.                                                                                                                                                                                                                              |                    |
| **Collaboration branch** (Kollaborationsbranch)                                 | Ihr Branch für die GitHub-Kollaboration, der für die Veröffentlichung verwendet wird. Standardmäßig ist dies „master“. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten.                                                                                                                                                                                                                                                               |                    |
| **Stammordner**                                          | Ihr Stammordner im Branch für die GitHub-Kollaboration.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Vorhandene Data Factory-Ressourcen in Repository importieren** | Gibt an, ob vorhandene Data Factory-Ressourcen aus der  **UX-Canvas für die Erstellung**  in ein GitHub-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Data Factory-Ressourcen in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln (d. h. die verknüpften Dienste und Datasets werden in separate JSONs exportiert). Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |
| **Branch zum Importieren der Ressource**                       | Gibt an, in welchen Branch die Data Factory-Ressourcen (Pipelines, Datasets, verknüpfte Dienste usw.) importiert werden. Sie können Ressourcen in einen der folgenden Branches importieren: a. Kollaboration b. Neu erstellen c. Vorhandene verwenden                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Konfigurationsmethode 2 (öffentliches Repository): UX-Canvas für die Erstellung

Suchen Sie in Azure Data Factory in der  **UX-Canvas für die Erstellung** nach Ihrer Data Factory. Wählen Sie das Dropdownmenü  **Data Factory**  und dann  **Coderepository konfigurieren** aus.

Ein Konfigurationsbereich wird angezeigt. Ausführliche Informationen zu den Konfigurationseinstellungen finden Sie in den obigen Beschreibungen zur  *Konfigurationsmethode 1*.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Konfigurieren eines GitHub Enterprise-Repositorys mit Azure Data Factory

Es gibt zwei Methoden zum Konfigurieren eines GitHub Enterprise-Repositorys mit einer Data Factory.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Konfigurationsmethode 1 (Enterprise-Repository): Seite „Erste Schritte“

Wechseln Sie in Azure Data Factory zur Seite  **Erste Schritte** . Wählen Sie  **Coderepository konfigurieren** aus:

![Seite „Erste Schritte“ von Data Factory](media/author-visually/github-integration-image1.png)

Der Konfigurationsbereich  **Repositoryeinstellungen**  wird angezeigt:

![GitHub-Repositoryeinstellungen](media/author-visually/github-integration-image3.png)

In dem Bereich werden die folgenden Einstellungen für das Coderepository für Azure Repos angezeigt:

| **Einstellung**                                              | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Wert**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Repositorytyp**                                      | Der Typ des Coderepositorys für Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub Enterprise verwenden**                                | Kontrollkästchen zum Auswählen von GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise-URL**                                | Die Stamm-URL von GitHub Enterprise. Beispiel: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub-Konto**                                       | Ihr GitHub-Kontoname. Sie finden diesen Namen unter https:\//github.com/{Kontoname}/{Repositoryname}. Beim Navigieren zu dieser Seite werden Sie zur Eingabe der GitHub-OAuth-Anmeldeinformationen zu Ihrem GitHub-Konto aufgefordert.                                                                                                                                                                                                                                               |                    |
| **Repositoryname**                                       | Der Name Ihres GitHub-Coderepositorys. GitHub-Konten enthalten Git-Repositorys zum Verwalten Ihres Quellcodes. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Konto befindet.                                                                                                                                                                                                                              |                    |
| **Collaboration branch** (Kollaborationsbranch)                                 | Ihr Branch für die GitHub-Kollaboration, der für die Veröffentlichung verwendet wird. Standardmäßig ist dies „master“. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten.                                                                                                                                                                                                                                                               |                    |
| **Stammordner**                                          | Ihr Stammordner im Branch für die GitHub-Kollaboration.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Vorhandene Data Factory-Ressourcen in Repository importieren** | Gibt an, ob vorhandene Data Factory-Ressourcen aus der  **UX-Canvas für die Erstellung**  in ein GitHub-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Data Factory-Ressourcen in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln (d. h. die verknüpften Dienste und Datasets werden in separate JSONs exportiert). Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |
| **Branch zum Importieren der Ressource**                       | Gibt an, in welchen Branch die Data Factory-Ressourcen (Pipelines, Datasets, verknüpfte Dienste usw.) importiert werden. Sie können Ressourcen in einen der folgenden Branches importieren: a. Kollaboration b. Neu erstellen c. Vorhandene verwenden                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Konfigurationsmethode 2 (Enterprise-Repository): UX-Canvas für die Erstellung

Suchen Sie in Azure Data Factory in der  **UX-Canvas für die Erstellung** nach Ihrer Data Factory. Wählen Sie das Dropdownmenü  **Data Factory**  und dann  **Coderepository konfigurieren** aus.

Ein Konfigurationsbereich wird angezeigt. Ausführliche Informationen zu den Konfigurationseinstellungen finden Sie in den obigen Beschreibungen zur  *Konfigurationsmethode 1*.

## <a name="use-the-expression-language"></a>Verwenden der Ausdruckssprache
Sie können Ausdrücke für Eigenschaftswerte angeben, indem sie die Ausdruckssprache verwenden, die von Azure Data Factory unterstützt wird.

Geben Sie Ausdrücke für Eigenschaftswerte an, indem Sie **Dynamischen Inhalt hinzufügen** auswählen:

![Verwenden der Ausdruckssprache](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Verwenden von Funktionen und Parametern

Sie können im **Ausdrucks-Generator** von Data Factory Funktionen verwenden oder Parameter für Pipelines und Datasets angeben:

Informationen zu den unterstützten Ausdrücken finden Sie unter [Ausdrücke und Funktionen in Azure Data Factory](control-flow-expression-language-functions.md).

![Dynamischen Inhalt hinzufügen](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Feedback geben
Wählen Sie **Feedback** aus, um Kommentare zu Funktionen abzugeben oder um Microsoft Probleme mit dem Tool zu melden:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nächste Schritte
Unter [Programmgesteuertes Überwachen und Verwalten von Pipelines](monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines.
