---
title: Quellcodeverwaltung in Synapse Studio
description: Erfahren Sie, wie Sie Quellcodeverwaltung in Azure Synapse Studio konfigurieren.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: b6cadbf5c3a33c1a954a47f37b33ad8703f40b69
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350737"
---
# <a name="source-control-in-azure-synapse-studio"></a>Quellcodeverwaltung in Azure Synapse Studio

Standardmäßig sind Bearbeitungen in Azure Synapse Studio direkt im Synapse-Dienst verfügbar. Für diese Erfahrung gelten jedoch die folgenden Einschränkungen:

- Synapse Studio enthält keinen temporären Speicher zum Speichern der Änderungen. Änderungen können nur über die Schaltfläche **Veröffentlichen** gespeichert und freigegeben werden, und sämtliche Änderungen werden direkt im Synapse-Dienst veröffentlicht.

- Synapse Studio ist nicht für Zusammenarbeit und Versionskontrolle optimiert.

Um die Quellcodeverwaltungs-Funktion bereitzustellen, ermöglicht Ihnen Synapse Studio, Ihren Arbeitsbereich einem Git-Repository, Azure DevOps oder GitHub zuzuordnen. In diesem Artikel wird erläutert, wie Sie in einem Synapse-Arbeitsbereich mit aktiviertem Git-Repository Konfigurationen vornehmen und arbeiten. Außerdem werden einige bewährte Methoden und eine Anleitung zur Problembehandlung vorgestellt.

> [!NOTE]
> Die Git-Integration von Azure Synapse Studio ist in der Azure Government-Cloud nicht verfügbar.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurieren des Git-Repositorys in Synapse Studio 

Nach dem Starten von Synapse Studio können Sie ein Git-Repository in Ihrem Arbeitsbereich konfigurieren. Ein Synapse Studio-Arbeitsbereich kann jeweils nur einem Git-Repository zugeordnet werden. 

### <a name="configuration-method-1-global-bar"></a>Konfigurationsmethode 1: globale Leiste

Wählen Sie in der globalen Leiste von Synapse Studio das Dropdownmenü **Synapse Live** und dann **Coderepository einrichten** aus.

![Konfigurieren der Coderepositoryeinstellungen im Erstellungsbereich](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Konfigurationsmethode 2: Verwaltungshub

Wechseln Sie zum Verwaltungshub von Synapse Studio. Wählen Sie **Git-Konfiguration** im Abschnitt **Quellcodeverwaltung** aus. Wenn kein Repository verbunden ist, klicken Sie auf **Konfigurieren**.

![Konfigurieren der Coderepositoryeinstellungen im Verwaltungshub](media/configure-repo-2.png)

> [!NOTE]
> Benutzer, denen die Rollen Mitwirkender, Besitzer oder Rollen höherer Ebene im Arbeitsbereich zugewiesen sind, können die Einstellung bearbeiten und die Verbindung mit dem Git-Repository in Azure Synapse Studio trennen. 

Sie können in Ihrem Arbeitsbereich entweder eine Verbindung mit dem Azure DevOps- oder GitHub-Git-Repository herstellen.

## <a name="connect-with-azure-devops-git"></a>Herstellen einer Verbindung mit dem Azure DevOps-Git-Repository 

Sie können einen Synapse-Arbeitsbereich einem Azure DevOps-Repository u. a. für die Quellcodeverwaltung, Zusammenarbeit und Versionsverwaltung zuordnen. Wenn Sie noch nicht über ein Azure DevOps-Repository verfügen, befolgen Sie [diese Anweisungen](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) zum Erstellen der Repositoryressourcen.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps-Git-Repositoryeinstellungen

Wenn Sie eine Verbindung mit dem Git-Repository herstellen, wählen Sie zuerst Azure DevOps-Git als Ihren Repositorytyp aus, wählen Sie dann in der Dropdownliste einen Azure AD-Mandanten aus, und klicken Sie auf **Weiter**.

![Konfigurieren der Repositoryeinstellungen](media/connect-with-azuredevops-repo-selected.png)

Im Konfigurationsbereich werden die folgenden Azure DevOps-Git-Einstellungen angezeigt:

| Einstellung | BESCHREIBUNG | Wert |
|:--- |:--- |:--- |
| **Repositorytyp** | Der Typ des Coderepositorys für Azure Repos.<br/> | Azure DevOps Git oder GitHub |
| **Azure Active Directory** | Ihr Name des Azure AD-Mandanten. | `<your tenant name>` |
| **Azure DevOps-Konto** | Der Name Ihrer Azure Repos-Organisation. Sie können den Namen Ihrer Azure Repos-Organisation unter `https://{organization name}.visualstudio.com` finden. Sie können sich [bei Ihrer Azure Repos-Organisation anmelden](https://www.visualstudio.com/team-services/git/), um auf Ihr Visual Studio-Profil zuzugreifen und Ihre Repositorys und Projekte anzuzeigen. | `<your organization name>` |
| **Projektname** | Der Name Ihres Azure Repos-Projekts. Sie können den Namen Ihres Azure Repos-Projekts unter `https://{organization name}.visualstudio.com/{project name}` finden. | `<your Azure Repos project name>` |
| **Repositoryname** | Der Name Ihres Azure Repos-Coderepositorys. Azure Repos-Projekte enthalten Git-Repositorys zum Verwalten Ihres Quellcodes, wenn Ihr Projekt umfangreicher wird. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Projekt befindet. | `<your Azure Repos code repository name>` |
| **Collaboration branch** (Kollaborationsbranch) | Ihr Branch für die Azure Repos-Kollaboration, der für die Veröffentlichung verwendet wird. Die Standardeinstellung ist `master`. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten. Sie können vorhandene Branchs auswählen oder neue erstellen. | `<your collaboration branch name>` |
| **Stammordner** | Ihr Stammordner im Branch für die Azure Repos-Kollaboration. | `<your root folder name>` |
| **Vorhandene Ressourcen in Repository importieren** | Gibt an, ob vorhandene Ressourcen aus Synapse Studio in ein Azure Repos Git-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Arbeitsbereichsressourcen (außer Pods) in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln. Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standard) |
| **Ressource in diesen Branch importieren** | Wählen Sie aus, in welchen Branch die Ressourcen (SQL-Skript, Notebook, Spark-Auftragsdefinition, Dataset, Dataflow usw.) importiert werden. 

Außerdem können Sie mit dem Repositorylink schnell auf das Git-Repository verweisen, mit dem Sie eine Verbindung herstellen möchten. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Verwenden eines anderen Azure Active Directory-Mandanten

Das Azure Repos-Git-Repository kann sich in einem anderen Azure Active Directory-Mandanten befinden. Zum Angeben eines anderen Azure AD-Mandanten müssen Sie über Administratorberechtigungen für das verwendete Azure-Abonnement verfügen. Weitere Informationen finden Sie unter [Ändern des Abonnementadministrators](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator).

> [!IMPORTANT]
> Um eine Verbindung mit einer anderen Azure Active Directory-Instanz herstellen zu können, muss der angemeldete Benutzer zu diesem Verzeichnis gehören. 

### <a name="use-your-personal-microsoft-account"></a>Verwenden Ihres persönlichen Microsoft-Kontos

Um ein persönliches Microsoft-Konto für die Git-Integration zu verwenden, können Sie Ihr persönliches Azure-Repository mit dem Active Directory Ihrer Organisation verknüpfen.

1. Fügen Sie Ihr persönliches Microsoft-Konto als Gast zum Active Directory Ihrer Organisation hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator).

2. Melden Sie sich mit Ihrem persönlichen Microsoft-Konto beim Azure-Portal an. Wechseln Sie dann zum Active Directory Ihrer Organisation.

3. Wechseln Sie zum Abschnitt für Azure DevOps – dort wird jetzt Ihr persönliches Repository angezeigt. Wählen Sie das Repository aus, und verbinden Sie es mit Active Directory.

Nach diesen Konfigurationsschritten ist Ihr persönliches Repository verfügbar, wenn Sie die Git-Integration in Synapse Studio einrichten.

Weitere Informationen zum Verbinden von Azure Repos mit dem Active Directory Ihrer Organisation finden Sie unter [Herstellen einer Verbindung Ihrer Organisation mit Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Herstellen einer Verbindung mit GitHub 

 Sie können einen Arbeitsbereich einem GitHub-Repository für die Quellcodeverwaltung, Zusammenarbeit und Versionsverwaltung zuordnen. Wenn Sie noch nicht über ein GitHub-Konto oder -Repository verfügen, folgen Sie [diesen Anweisungen](https://github.com/join) zum Erstellen der Ressourcen.

Die GitHub-Integration in Synapse Studio unterstützt sowohl das öffentliche GitHub (also [https://github.com](https://github.com)) als auch GitHub Enterprise. Sie können öffentliche und private GitHub-Repositorys verwenden, sofern Sie über Lese- und Schreibberechtigungen für das Repository in GitHub verfügen.

### <a name="github-settings"></a>GitHub-Einstellungen

Wenn Sie eine Verbindung mit dem Git-Repository herstellen, wählen Sie zuerst GitHub als Ihren Repositorytyp aus, und geben Sie dann Ihr GitHub-Konto oder eine GitHub Enterprise Server-URL an, wenn Sie GitHub Enterprise Server verwenden, und klicken Sie auf **Weiter**.

![GitHub-Repositoryeinstellungen](media/connect-with-github-repo-1.png)

Im Konfigurationsbereich werden die folgenden Einstellungen für das GitHub-Repository angezeigt:

| **Einstellung** | **Beschreibung**  | **Wert**  |
|:--- |:--- |:--- |
| **Repositorytyp** | Der Typ des Coderepositorys für Azure Repos. | GitHub |
| **GitHub Enterprise verwenden** | Kontrollkästchen zum Auswählen von GitHub Enterprise | nicht ausgewählt (Standard) |
| **GitHub Enterprise-URL** | Die GitHub Enterprise-URL des Stamms (muss für den lokalen GitHub Enterprise-Server „HTTPS“ lauten). Beispiel: `https://github.mydomain.com`. Nur erforderlich, wenn **GitHub Enterprise verwenden** ausgewählt ist | `<your GitHub enterprise url>` |                                                           
| **GitHub-Konto** | Ihr GitHub-Kontoname. Sie finden diesen Namen unter https:\//github.com/{Kontoname}/{Repositoryname}. Beim Navigieren zu dieser Seite werden Sie zur Eingabe der GitHub-OAuth-Anmeldeinformationen zu Ihrem GitHub-Konto aufgefordert. | `<your GitHub account name>` |
| **Repositoryname**  | Der Name Ihres GitHub-Coderepositorys. GitHub-Konten enthalten Git-Repositorys zum Verwalten Ihres Quellcodes. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Konto befindet. | `<your repository name>` |
| **Collaboration branch** (Kollaborationsbranch) | Ihr Branch für die GitHub-Kollaboration, der für die Veröffentlichung verwendet wird. Die Standardeinstellung ist „master“. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten. | `<your collaboration branch>` |
| **Stammordner** | Ihr Stammordner im Branch für die GitHub-Kollaboration. |`<your root folder name>` |
| **Vorhandene Ressourcen in Repository importieren** | Gibt an, ob vorhandene Ressourcen aus Synapse Studio in ein Git-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Arbeitsbereichsressourcen (außer Pods) in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln. Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |
| **Ressource in diesen Branch importieren** | Wählen Sie aus, in welchen Branch die Ressourcen (SQL-Skript, Notebook, Spark-Auftragsdefinition, Dataset, Dataflow usw.) importiert werden. 

### <a name="github-organizations"></a>GitHub-Organisationen

Um eine Verbindung mit einer GitHub-Organisation herstellen zu können, muss die Organisation eine Berechtigung für Synapse Studio erteilen. Ein Benutzer mit Administratorberechtigungen in der Organisation muss die folgenden Schritte ausführen.

#### <a name="connecting-to-github-for-the-first-time"></a>Erstmaliges Herstellen einer Verbindung mit GitHub

Wenn Sie zum ersten Mal von Synapse Studio aus eine Verbindung mit GitHub herstellen, führen Sie die folgenden Schritte aus, um eine Verbindung mit einer GitHub-Organisation herzustellen.

1. Geben Sie im Bereich für die Git-Konfiguration den Namen der Organisation im Feld *GitHub-Konto* ein. Es wird eine Eingabeaufforderung zur Anmeldung bei GitHub angezeigt. 

1. Melden Sie sich mit Ihren Benutzeranmeldeinformationen an.

1. Sie werden aufgefordert, Synapse als Anwendung namens *Azure Synapse* zu autorisieren. Auf diesem Bildschirm sehen Sie eine Option, mit der Sie Synapse die Berechtigung zum Zugriff auf die Organisation erteilen können. Wenn diese Option nicht angezeigt wird, bitten Sie einen Administrator, die Berechtigung manuell über GitHub zu erteilen.

Nachdem Sie diese Schritte ausgeführt haben, kann Ihr Arbeitsbereich eine Verbindung sowohl mit den privaten als auch öffentlichen Repositorys in Ihrer Organisation herstellen. Wenn keine Verbindung hergestellt werden kann, löschen Sie den Browsercache und versuchen es noch mal.

## <a name="version-control"></a>Versionskontrolle

Versionskontrollsysteme (auch als _Quellcodeverwaltung_ bezeichnet) ermöglichen Entwicklern die Zusammenarbeit an Code und das Nachverfolgen von Änderungen. Die Quellcodeverwaltung ist ein wichtiges Tool für Projekte, an denen mehrere Entwickler arbeiten.

### <a name="creating-feature-branches"></a>Erstellen von Featurebranches

Jedes Git-Repository, das einer Synapse Studio-Instanz zugeordnet ist, verfügt über einen Branch für die Zusammenarbeit. (`main` oder `master` ist der Standardbranch für die Kollaboration.) Benutzer können auch Featurebranches erstellen, indem sie im Dropdownmenü „Branch“ auf **+ Neuer Branch** klicken. Sobald der Bereich „Neuer Branch“ angezeigt wird, geben Sie den Namen Ihres Featurebranches ein.

![Neuen Branch erstellen](media/create-new-branch.png)

Wenn Sie bereit sind, die Änderungen in Ihrem Featurebranch mit Ihrem Kollaborationsbranch zu mergen, klicken Sie auf das Dropdownmenü „Branch“ und dann auf **Pull Request erstellen**. Sie gelangen zum Git-Anbieter, wo Sie Pullanforderungen auslösen, Codereviews durchführen und Änderungen an Ihrem Kollaborationsbranch zusammenführen können. Sie können nur Veröffentlichungen für den Synapse-Dienst Ihres Kollaborationsbranchs durchführen. 

![Erstellen eines neuen Pull Requests](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurieren von Veröffentlichungseinstellungen

Synapse Studio generiert standardmäßig die Arbeitsbereichsvorlagen und speichert sie in einem Branch mit dem Namen `workspace_publish`. Um einen benutzerdefinierten Veröffentlichungsbranch zu konfigurieren, fügen Sie dem Stammordner im Kollaborationsbranch die Datei `publish_config.json` hinzu. Beim Veröffentlichen liest Synapse Studio diese Datei, sucht nach dem Feld `publishBranch` und speichert Arbeitsbereichsvorlagen-Dateien am angegebenen Speicherort. Wenn der Branch nicht vorhanden ist, wird er von Synapse Studio automatisch erstellt. Das nachstehende Beispiel zeigt, wie diese Datei aussieht:

```json
{
    "publishBranch": "workspace_publish"
}
```

In Azure Synapse Studio kann jeweils nur ein Veröffentlichungsbranch vorhanden sein. Wenn Sie einen neuen Branch für die Veröffentlichung angeben, wird der vorherige Branch für die Veröffentlichung nicht gelöscht. Wenn Sie den vorherigen Branch für die Veröffentlichung entfernen möchten, löschen Sie ihn manuell.

### <a name="publish-code-changes"></a>Veröffentlichen von Codeänderungen

Nachdem Sie die Änderungen mit dem Kollaborationsbranch gemergt haben, klicken Sie auf **Veröffentlichen**, um Ihre Codeänderungen manuell im Kollaborationsbranch im Synapse-Dienst zu veröffentlichen.

![Veröffentlichen von Änderungen](media/gitmode-publish.png)

Ein Seitenbereich wird geöffnet, in dem Sie bestätigen, dass der Branch für die Veröffentlichung und ausstehende Änderungen korrekt sind. Nachdem Sie Ihre Änderungen überprüft haben, klicken Sie auf **OK**, um die Veröffentlichung zu bestätigen.

![Überprüfen des Branch für die Veröffentlichung](media/publish-change.png)

> [!IMPORTANT]
> Der Kollaborationsbranch ist nicht repräsentativ für das, was im Dienst bereitgestellt wird. Die Änderungen im Kollaborationsbranch *müssen* manuell im Dienst veröffentlicht werden.

## <a name="switch-to-a-different-git-repository"></a>Wechseln zu einem anderen Git-Repository

Wenn Sie zu einem anderen Git-Repository wechseln möchten, navigieren Sie im Verwaltungshub unter **Quellcodeverwaltung** zur Seite mit der Git-Konfiguration. Wählen Sie **Trennen** aus. 

![Git-Symbol](media/remove-repository.png)

Geben Sie Ihren Arbeitsbereichsnamen ein, und klicken Sie auf **Trennen**, um das Git-Repository zu entfernen, das Ihrem Arbeitsbereich zugeordnet ist.

Nachdem Sie die Zuordnung zum aktuellen Repository entfernt haben, können Sie Ihre Git-Einstellungen zur Verwendung eines anderen Repositorys konfigurieren und dann vorhandene Ressourcen in das neue Repository importieren.

> [!IMPORTANT]
> Beim Entfernen einer Git-Konfiguration aus einem Arbeitsbereich wird nichts aus dem Repository gelöscht. Der Synapse-Arbeitsbereich enthält alle veröffentlichten Ressourcen. Sie können den Arbeitsbereich weiterhin direkt für den Dienst bearbeiten.

## <a name="best-practices-for-git-integration"></a>Bewährte Methoden für die Git-Integration

-   **Berechtigungen** Sobald Sie über ein Git-Repository verfügen, das mit Ihrem Arbeitsbereich verbunden ist, kann jeder Benutzer, der mit einer beliebigen Rolle Zugriff auf das Git-Repository in Ihrem Arbeitsbereich hat, Artefakte wie SQL-Skript, Notebook, Definition des Spark-Auftrags, Dataset, Datenfluss und Pipeline im Git-Modus aktualisieren. Normalerweise möchten Sie nicht, dass jedes Teammitglied die Berechtigung zum Aktualisieren des Arbeitsbereichs hat. Erteilen Sie die Git-Repositoryberechtigung nur den Artefaktautoren von Synapse. 
-   **Zusammenarbeit**. Es wird empfohlen, keine direkten Eincheckvorgänge im Kollaborationsbranch zuzulassen. Diese Einschränkung kann dazu beitragen, Fehler zu vermeiden, da jeder Eincheckvorgang einen Pull Request-Prozess durchläuft, der unter [Erstellen von Featurebranches](source-control.md#creating-feature-branches) beschrieben ist.
-   **Synapse-Livemodus**. Nach dem Veröffentlichen im Git-Modus werden alle Änderungen im Synapse-Livemodus angezeigt. Im Synapse-Livemodus ist die Veröffentlichung deaktiviert. Wenn Sie die richtige Berechtigung erhalten haben, können Sie außerdem Artefakte im Livemodus anzeigen und ausführen. 
-   **Bearbeiten von Artefakten in Studio**. Nur in Synapse Studio können Sie die Quellcodeverwaltung des Arbeitsbereichs aktivieren und Änderungen automatisch mit Git synchronisieren. Jede Änderung über das SDK oder PowerShell wird nicht mit Git synchronisiert. Sie sollten Artefakte immer in Studio bearbeiten, wenn Git aktiviert ist.

## <a name="troubleshooting-git-integration"></a>Problembehandlung bei der Git-Integration

### <a name="access-to-git-mode"></a>Zugriff auf den Git-Modus 

Wenn Ihnen die Berechtigung für das GitHub-Git-Repository erteilt wurde, das mit Ihrem Arbeitsbereich verknüpft ist, Sie aber nicht auf den Git-Modus zugreifen können: 

1. Löschen Sie Ihren Cache und aktualisieren Sie die Seite. 

1. Melden Sie sich bei Ihrem GitHub-Konto an.

### <a name="stale-publish-branch"></a>Veralteter Branch für die Veröffentlichung

Wenn der Branch für die Veröffentlichung nicht mit dem Kollaborationsbranch synchron ist und trotz einer aktuellen Veröffentlichung veraltete Ressourcen enthält, versuchen Sie die folgenden Schritte:

1. Entfernen des aktuellen Git-Repositorys

1. Konfigurieren Sie Git mit denselben Einstellungen neu. Stellen Sie jedoch sicher, dass **Vorhandene Ressourcen in Repository importieren** ausgewählt ist, und wählen Sie denselben Branch aus.  

1. Erstellen eines Pull Request zum Mergen der Änderungen in den Kollaborationsbranch 

## <a name="unsupported-features"></a>Nicht unterstützte Funktionen

- Standardmäßig lässt Synapse Studio ein Cherrypicking von Commits oder die selektive Veröffentlichung von Ressourcen nicht zu. 
- Synapse Studio unterstützt die Anpassung der Commitnachricht nicht.
- Entwurfsbedingt wird die Löschaktion in Studio direkt an Git committet.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Implementieren von Continuous Integration und Deployment finden Sie unter [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-deployment.md).
