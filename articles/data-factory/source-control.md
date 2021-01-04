---
title: Quellcodeverwaltung
description: Erfahren Sie, wie Sie die Quellcodeverwaltung in Azure Data Factory konfigurieren
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: d219ff8469c471a37deb47e0f217292d70e8f0f9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497111"
---
# <a name="source-control-in-azure-data-factory"></a>Quellcodeverwaltung in Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Standardmäßig erfolgt die Erstellung mit der Azure Data Factory-Benutzeroberfläche (UX) direkt für den Data Factory-Dienst. Für diese Umgebung gelten die folgenden Einschränkungen:

- Der Data Factory-Dienst umfasst kein Repository zum Speichern der JSON-Entitäten für Ihre Änderungen. Änderungen können nur über die Schaltfläche **Alle veröffentlichen** gespeichert werden, und sämtliche Änderungen werden direkt im Data Factory-Dienst veröffentlicht.
- Der Data Factory-Dienst ist nicht für Kollaboration oder Versionskontrolle optimiert.

Um eine bessere Erstellung zu ermöglichen, können Sie mit Azure Data Factory ein Git-Repository mit Azure Repos oder GitHub konfigurieren. Git ist ein Versionskontrollsystem, das eine einfachere Änderungsnachverfolgung und Kollaboration ermöglicht. In diesem Artikel wird erläutert, wie Sie ein Git-Repository konfigurieren und in diesem arbeiten. Außerdem finden Sie hier Best Practices und eine Anleitung zur Problembehandlung.

> [!NOTE]
> Die Git-Integration von Azure Data Factory ist in der Azure Government-Cloud nicht verfügbar.

Um zu erfahren, wie die Integration von Azure Data Factory in Git funktioniert, sehen Sie sich das folgende 15-minütige Videotutorial an:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Vorteile der Git-Integration

Im Folgenden finden Sie eine Liste einiger der Vorteile der Git-Integration für die Erstellung:

-   **Quellcodeverwaltung:** Wenn Ihre Data Factory-Workloads sehr wichtig sind, sollten Sie Ihre Factory in Git integrieren, um verschiedene Vorteile der Quellcodeverwaltung wie die folgenden nutzen zu können:
    -   Möglichkeit zum Nachverfolgen/Überwachen von Änderungen
    -   Möglichkeit zum Rückgängigmachen von Änderungen, die Fehler verursacht haben
-   **Partielle Speicherungen:** Wenn Sie eine Erstellung für den Data Factory-Dienst durchführen, können Sie Änderungen nicht als Entwurf speichern, und alle Veröffentlichungen müssen die Data Factory-Validierung durchlaufen. Falls Ihre Pipelines noch nicht fertig sind oder Sie einfach im Fall eines Computerabsturzes keine Änderungen verlieren möchten, ermöglicht die Git-Integration inkrementelle Änderungen von Data Factory-Ressourcen unabhängig von ihrem Zustand. Durch das Konfigurieren eines Git-Repositorys können Sie Änderungen speichern und die Veröffentlichung erst dann durchführen, wenn Sie Ihre Änderungen zu Ihrer Zufriedenheit getestet haben.
-   **Kollaboration und Steuerung:** Wenn mehrere Ihrer Teammitglieder Beiträge zur selben Factory liefern, empfiehlt es sich, Ihre Teamkollegen über einen Code Review-Prozess miteinander zusammenarbeiten zu lassen. Sie können die Factory auch so einrichten, dass nicht alle Mitwirkenden über die gleichen Berechtigungen verfügen. Einige Teammitglieder sind möglicherweise nur berechtigt, Änderungen über Git vorzunehmen, und nur bestimmte Personen im Team dürfen die Änderungen an der Factory veröffentlichen.
-   **Bessere CI/CD:**  Wenn Sie eine Bereitstellung in mehreren Umgebungen mit einem [Continuous Delivery-Prozess](continuous-integration-deployment.md) durchführen, werden bestimmte Aktionen durch die Git-Integration vereinfacht. Zu diesen Aktionen zählen beispielsweise folgende:
    -   Konfigurieren Ihrer Releasepipeline, sodass sie automatisch ausgelöst wird, sobald Änderungen an der Entwicklungsfactory vorgenommen wurden.
    -   Anpassen der Eigenschaften in Ihrer Factory, die als Parameter in der Resource Manager-Vorlage verfügbar sind. Es kann hilfreich sein, nur die erforderlichen Eigenschaften als Parameter beizubehalten und alles andere fest zu codieren.
-   **Höhere Leistung:** Im Durchschnitt wird eine Factory mit Git-Integration 10-mal schneller geladen als eine für die Erstellung für den Data Factory-Dienst. Diese Leistungsverbesserung liegt daran, dass Ressourcen über Git heruntergeladen werden.

> [!NOTE]
> Das direkte Erstellen mit dem Data Factory-Dienst wird auf der Azure Data Factory-Benutzeroberfläche deaktiviert, wenn ein Git-Repository konfiguriert ist. Änderungen, die über PowerShell oder ein SDK vorgenommen wurden, werden direkt im Data Factory-Dienst veröffentlicht und nicht bei Git eingegeben.

## <a name="connect-to-a-git-repository"></a>Herstellen einer Verbindung mit einem Git-Repository

Es gibt vier verschiedene Möglichkeiten, eine Verbindung zwischen einem Git-Repository und Ihrer Data Factory herzustellen – sowohl für Azure Repos als auch für GitHub. Nachdem Sie eine Verbindung mit einem Git-Repository hergestellt haben, können Sie Ihre Konfiguration im [Verwaltungshub](author-management-hub.md) unter **Git-Konfiguration** im Abschnitt **Quellcodeverwaltung** anzeigen und verwalten.

### <a name="configuration-method-1-home-page"></a>Konfigurationsmethode 1: Startseite

Wählen Sie auf der Homepage von Azure Data Factory **Coderepository einrichten** aus.

![Konfigurieren eines Coderepositorys auf der Homepage](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Konfigurationsmethode 2: Erstellungsbereich

Wählen Sie auf der Azure Data Factory-Benutzeroberfläche im Erstellungsbereich das Dropdownmenü **Data Factory** und dann **Coderepository einrichten** aus.

![Konfigurieren der Coderepositoryeinstellungen im Erstellungsbereich](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Konfigurationsmethode 3: Verwaltungshub

Wechseln Sie auf der ADF-Benutzeroberfläche zum Verwaltungshub. Wählen Sie **Git-Konfiguration** im Abschnitt **Quellcodeverwaltung** aus. Wenn keine Repository verbunden ist, klicken Sie auf **Coderepository einrichten**.

![Konfigurieren der Coderepositoryeinstellungen im Verwaltungshub](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Konfigurationsmethode 4: Während der Erstellung einer Factory

Wenn Sie im Azure-Portal eine neue Data Factory erstellen, können Sie auf der Registerkarte **Git-Konfiguration** Informationen zum Git-Repository konfigurieren.

> [!NOTE]
> Beim Konfigurieren von Git im Azure-Portal müssen Einstellungen wie Projektname und Repositoryname manuell eingegeben werden, da keine Dropdownliste zur Verfügung steht.

![Konfigurieren der Coderepositoryeinstellungen im Azure-Portal](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Erstellen mit der Azure Repos Git-Integration

Das visuelle Erstellen mit der Azure Repos Git-Integration unterstützt die Quellcodeverwaltung und Kollaboration beim Arbeiten an Data Factory-Pipelines. Sie können eine Data Factory einem Azure Repos Git-Organisationsrepository u.a. für die Quellcodeverwaltung, Kollaboration, Versionsverwaltung zuordnen. Eine einzelne Azure Repos Git-Organisation kann über mehrere Repositorys verfügen. Allerdings kann ein Azure Repos Git-Repository nur einer einzigen Data Factory zugeordnet werden. Wenn Sie noch nicht über eine Azure Repos-Organisation oder ein -Repository verfügen, folgen Sie [diesen Anweisungen](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) zum Erstellen der Ressourcen.

> [!NOTE]
> Sie können Skript- und Datendateien in einem Azure Repos Git-Repository speichern. Allerdings müssen Sie die Dateien manuell in Azure Storage hochladen. Eine Data Factory-Pipeline lädt Skript- oder Datendateien, die in einem Azure Repos Git-Repository gespeichert sind, nicht automatisch in Azure Storage hoch.

### <a name="azure-repos-settings"></a>Azure Repos-Einstellungen

![Konfigurieren der Repositoryeinstellungen](media/author-visually/repo-settings.png)

Im Konfigurationsbereich werden die folgenden Einstellungen für das Coderepository für Azure Repos angezeigt:

| Einstellung | Beschreibung | Wert |
|:--- |:--- |:--- |
| **Repositorytyp** | Der Typ des Coderepositorys für Azure Repos.<br/> | Azure DevOps Git oder GitHub |
| **Azure Active Directory** | Ihr Name des Azure AD-Mandanten. | `<your tenant name>` |
| **Azure Repos-Organisation** | Der Name Ihrer Azure Repos-Organisation. Sie können den Namen Ihrer Azure Repos-Organisation unter `https://{organization name}.visualstudio.com` finden. Sie können sich [bei Ihrer Azure Repos-Organisation anmelden](https://www.visualstudio.com/team-services/git/), um auf Ihr Visual Studio-Profil zuzugreifen und Ihre Repositorys und Projekte anzuzeigen. | `<your organization name>` |
| **Projektname** | Der Name Ihres Azure Repos-Projekts. Sie können den Namen Ihres Azure Repos-Projekts unter `https://{organization name}.visualstudio.com/{project name}` finden. | `<your Azure Repos project name>` |
| **Repositoryname** | Der Name Ihres Azure Repos-Coderepositorys. Azure Repos-Projekte enthalten Git-Repositorys zum Verwalten Ihres Quellcodes, wenn Ihr Projekt umfangreicher wird. Sie können ein neues Repository erstellen oder ein vorhandenes Repository verwenden, das sich bereits in Ihrem Projekt befindet. | `<your Azure Repos code repository name>` |
| **Collaboration branch** (Kollaborationsbranch) | Ihr Branch für die Azure Repos-Kollaboration, der für die Veröffentlichung verwendet wird. Die Standardeinstellung ist `master`. Ändern Sie diese Einstellung, falls Sie Ressourcen eines anderen Branchs veröffentlichen möchten. | `<your collaboration branch name>` |
| **Stammordner** | Ihr Stammordner im Branch für die Azure Repos-Kollaboration. | `<your root folder name>` |
| **Vorhandene Data Factory-Ressourcen in Repository importieren** | Gibt an, ob vorhandene Data Factory-Ressourcen aus dem **Dokumenterstellungsbereich** in ein Azure Repos Git-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Data Factory-Ressourcen in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln (d. h. die verknüpften Dienste und Datasets werden in separate JSONs exportiert). Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |
| **Branch zum Importieren der Ressource** | Gibt an, in welchen Branch die Data Factory-Ressourcen (Pipelines, Datasets, verknüpfte Dienste usw.) importiert werden. Sie können Ressourcen in einen der folgenden Branches importieren: a. Kollaboration b. Neu erstellen c. Vorhandene verwenden |  |

> [!NOTE]
> Wenn Sie Microsoft Edge verwenden und in der Dropdownliste Ihres Azure DevOps-Kontos keine Werte sehen, fügen Sie https://*.visualstudio.com zur Liste der vertrauenswürdigen Sites hinzu.

### <a name="use-a-different-azure-active-directory-tenant"></a>Verwenden eines anderen Azure Active Directory-Mandanten

Das Azure Repos-Git-Repository kann sich in einem anderen Azure Active Directory-Mandanten befinden. Zum Angeben eines anderen Azure AD-Mandanten müssen Sie über Administratorberechtigungen für das verwendete Azure-Abonnement verfügen. Weitere Informationen finden Sie unter [Ändern des Abonnementadministrators](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator).

> [!IMPORTANT]
> Um eine Verbindung mit einer anderen Azure Active Directory-Instanz herstellen zu können, muss der angemeldete Benutzer zu diesem Verzeichnis gehören. 

### <a name="use-your-personal-microsoft-account"></a>Verwenden Ihres persönlichen Microsoft-Kontos

Um ein persönliches Microsoft-Konto für die Git-Integration zu verwenden, können Sie Ihr persönliches Azure-Repository mit dem Active Directory Ihrer Organisation verknüpfen.

1. Fügen Sie Ihr persönliches Microsoft-Konto als Gast zum Active Directory Ihrer Organisation hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

2. Melden Sie sich mit Ihrem persönlichen Microsoft-Konto beim Azure-Portal an. Wechseln Sie dann zum Active Directory Ihrer Organisation.

3. Wechseln Sie zum Abschnitt für Azure DevOps – dort wird jetzt Ihr persönliches Repository angezeigt. Wählen Sie das Repository aus, und verbinden Sie es mit Active Directory.

Nach diesen Konfigurationsschritten ist Ihr persönliches Repository verfügbar, wenn Sie die Git-Integration auf der Data Factory-Benutzeroberfläche einrichten.

Weitere Informationen zum Verbinden von Azure Repos mit dem Active Directory Ihrer Organisation finden Sie unter [Verbinden Ihrer Azure DevOps-Organisation mit Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Erstellen der GitHub-Integration

Das visuelle Erstellen mit GitHub-Integration unterstützt Quellcodeverwaltung und Kollaboration beim Arbeiten an den Data Factory-Pipelines. Sie können eine Data Factory einem GitHub-Kontorepository für die Quellcodeverwaltung, Kollaboration und Versionsverwaltung zuordnen. Ein einzelnes GitHub-Konto kann über mehrere Repositorys verfügen. Allerdings kann ein GitHub-Repository nur einer einzigen Data Factory zugeordnet werden. Wenn Sie noch nicht über ein GitHub-Konto oder -Repository verfügen, folgen Sie [diesen Anweisungen](https://github.com/join) zum Erstellen der Ressourcen.

Die GitHub-Integration von Data Factory unterstützt sowohl das öffentliche GitHub (also [https://github.com](https://github.com)) als auch GitHub Enterprise. Sie können öffentliche und private GitHub-Repositorys mit Data Factory verwenden, sofern Sie über Lese- und Schreibberechtigungen für das Repository in GitHub verfügen.

Zum Konfigurieren eines GitHub-Repositorys müssen Sie über Administratorberechtigungen für das verwendete Azure-Abonnement verfügen.

### <a name="github-settings"></a>GitHub-Einstellungen

![GitHub-Repositoryeinstellungen](media/author-visually/github-integration-image2.png)

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
| **Vorhandene Data Factory-Ressourcen in Repository importieren** | Gibt an, ob vorhandene Data Factory-Ressourcen aus dem Erstellungsbereich auf der Benutzeroberfläche in ein GitHub-Repository importiert werden sollen. Aktivieren Sie das Kontrollkästchen, um Ihre Data Factory-Ressourcen in das zugehörige Git-Repository im JSON-Format zu importieren. Diese Aktion exportiert jede Ressource einzeln (d. h. die verknüpften Dienste und Datasets werden in separate JSONs exportiert). Ist dieses Kontrollkästchen nicht aktiviert, werden die vorhandenen Ressourcen nicht importiert. | Aktiviert (Standardeinstellung) |
| **Branch zum Importieren der Ressource** | Gibt an, in welchen Branch die Data Factory-Ressourcen (Pipelines, Datasets, verknüpfte Dienste usw.) importiert werden. Sie können Ressourcen in einen der folgenden Branches importieren: a. Kollaboration b. Neu erstellen c. Vorhandene verwenden |  |

### <a name="github-organizations"></a>GitHub-Organisationen

Um eine Verbindung mit einer GitHub-Organisation herstellen zu können, muss die Organisation eine Berechtigung für Azure Data Factory erteilen. Ein Benutzer mit Administratorberechtigungen in der Organisation muss die folgenden Schritte ausführen, damit Data Factory eine Verbindung herstellen kann.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Erstmaliges Herstellen einer Verbindung mit GitHub in Azure Data Factory

Wenn Sie zum ersten Mal eine Verbindung zwischen Azure Data Factory und GitHub herstellen, führen Sie die folgenden Schritte aus, um eine Verbindung mit einer GitHub-Organisation herzustellen.

1. Geben Sie im Bereich für die Git-Konfiguration den Namen der Organisation im Feld *GitHub-Konto* ein. Es wird eine Eingabeaufforderung zur Anmeldung bei GitHub angezeigt. 
1. Melden Sie sich mit Ihren Benutzeranmeldeinformationen an.
1. Sie werden aufgefordert, Azure Data Factory als Anwendung namens *AzureDataFactory* zu autorisieren. Auf diesem Bildschirm sehen Sie eine Option, mit der Sie ADF die Berechtigung zum Zugriff auf die Organisation erteilen können. Wenn diese Option nicht angezeigt wird, bitten Sie einen Administrator, die Berechtigung manuell über GitHub zu erteilen.

Nachdem Sie diese Schritte ausgeführt haben, kann Ihre Factory eine Verbindung sowohl mit den privaten als auch mit den öffentlichen Repositorys in Ihrer Organisation herstellen. Wenn keine Verbindung hergestellt werden kann, löschen Sie den Browsercache und versuchen es noch mal.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Bereits über ein persönliches Konto mit GitHub verbunden

Wenn Sie bereits mit GitHub verbunden sind und nur Berechtigungen zum Zugriff auf ein persönliches Konto erteilt haben, führen Sie die folgenden Schritte aus, um Berechtigungen für eine Organisation zu erteilen. 

1. Wechseln Sie zu GitHub, und öffnen Sie die **Einstellungen**.

    ![Öffnen der GitHub-Einstellungen](media/author-visually/github-settings.png)

1. Wählen Sie **Anwendungen** aus. Auf der Registerkarte **Autorisierte OAuth-Apps** sollte *AzureDataFactory* angezeigt werden.

    ![Auswählen von OAuth-Apps](media/author-visually/github-organization-select-application.png)

1. Wählen Sie die Anwendung aus, und erteilen Sie ihr Zugriff auf Ihre Organisation.

    ![Gewähren von Zugriff](media/author-visually/github-organization-grant.png)

Nachdem Sie diese Schritte ausgeführt haben, kann Ihre Factory eine Verbindung sowohl mit den privaten als auch mit den öffentlichen Repositorys in Ihrer Organisation herstellen. 

### <a name="known-github-limitations"></a>Bekannte GitHub-Einschränkungen

- Sie können Skript- und Datendateien in einem GitHub-Repository speichern. Allerdings müssen Sie die Dateien manuell in Azure Storage hochladen. Eine Data Factory-Pipeline lädt Skript- oder Datendateien, die in einem GitHub-Repository gespeichert sind, nicht automatisch in Azure Storage hoch.

- GitHub Enterprise-Versionen vor 2.14.0 funktionieren nicht im Browser Microsoft Edge.

- Die GitHub-Integration in die visuellen Data Factory-Erstellungstools funktioniert nur in der allgemein verfügbaren Version von Data Factory.


- Von einer einzelnen GitHub-Verzweigung können maximal 1.000 Entitäten pro Ressourcentyp wie Pipelines und Datasets abgerufen werden. Sobald diese Begrenzung erreicht wurde, sollten Sie Ihre Ressourcen in einzelne Factorys aufteilen. Azure DevOps-Git-Repositorys unterliegen dieser Einschränkung nicht.

## <a name="version-control"></a>Versionskontrolle

Versionskontrollsysteme (auch als _Quellcodeverwaltung_ bezeichnet) ermöglichen Entwicklern die Zusammenarbeit an Code und das Nachverfolgen von an der Codebasis vorgenommenen Änderungen. Quellcodeverwaltung ist ein unverzichtbares Tool für Projekte, an denen mehrere Entwickler arbeiten.

### <a name="creating-feature-branches"></a>Erstellen von Featurebranches

Jedes Azure Repos Git-Repository, das einer Data Factory zugeordnet ist, verfügt über einen Branch für die Kollaboration. (`main` ist der Standardbranch für die Kollaboration.) Benutzer können auch Featurebranches erstellen, indem sie im Dropdownmenü „Branch“ auf **+ Neuer Branch** klicken. Sobald der Bereich „Neuer Branch“ angezeigt wird, geben Sie den Namen Ihres Featurebranches ein.

![Neuen Branch erstellen](media/author-visually/new-branch.png)

Wenn Sie bereit sind, die Änderungen in Ihrem Featurebranch mit Ihrem Kollaborationsbranch zu mergen, klicken Sie auf das Dropdownmenü „Branch“ und dann auf **Pull Request erstellen**. Dann gelangen Sie zu Azure Repos Git und können Pull Requests auslösen, Codereviews durchführen und Änderungen an Ihrem Kollaborationsbranch zusammenführen. (`master` ist die Standardeinstellung.) Sie können nur Veröffentlichungen für den Data Factory-Dienst Ihres Kollaborationsbranchs durchführen. 

![Erstellen eines neuen Pull Requests](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurieren von Veröffentlichungseinstellungen

Standardmäßig generiert Data Factory die Resource Manager-Vorlagen der veröffentlichten Factory und speichert sie in einem Branch mit dem Namen `adf_publish`. Um einen benutzerdefinierten Veröffentlichungsbranch zu konfigurieren, fügen Sie dem Stammordner im Kollaborationsbranch die Datei `publish_config.json` hinzu. Beim Veröffentlichen liest ADF diese Datei, sucht nach dem Feld `publishBranch` und speichert alle Resource Manager-Vorlagen am angegebenen Speicherort. Wenn der Branch nicht vorhanden ist, wird er von Data Factory automatisch erstellt. Das nachstehende Beispiel zeigt, wie diese Datei aussieht:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

In Azure Data Factory kann jeweils nur ein Veröffentlichungsbranch vorhanden sein. Wenn Sie einen neuen Branch für die Veröffentlichung angeben, löscht die Data Factory den vorherigen Branch für die Veröffentlichung nicht. Wenn Sie den vorherigen Branch für die Veröffentlichung entfernen möchten, löschen Sie ihn manuell.

> [!NOTE]
> Die Data Factory liest die `publish_config.json`-Datei nur, wenn diese die Factory lädt. Wenn die Factory bereits im Portal geladen ist, aktualisieren Sie den Browser, damit Ihre Änderungen übernommen werden.

### <a name="publish-code-changes"></a>Veröffentlichen von Codeänderungen

Nachdem Sie die Änderungen mit dem Kollaborationsbranch gemergt haben (`master` ist die Standardeinstellung), klicken Sie auf **Veröffentlichen**, um Ihre Codeänderungen manuell im Masterbranch im Data Factory-Dienst zu veröffentlichen.

![Veröffentlichen von Änderungen für den Data Factory-Dienst](media/author-visually/publish-changes.png)

Ein Seitenbereich wird geöffnet, in dem Sie bestätigen, dass der Branch für die Veröffentlichung und ausstehende Änderungen korrekt sind. Nachdem Sie Ihre Änderungen überprüft haben, klicken Sie auf **OK**, um die Veröffentlichung zu bestätigen.

![Überprüfen des Branch für die Veröffentlichung](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Der Masterbranch ist nicht repräsentativ für das, was im Data Factory-Dienst bereitgestellt wird. Der Masterbranch *muss* manuell im Data Factory-Dienst veröffentlicht werden.

## <a name="best-practices-for-git-integration"></a>Bewährte Methoden für die Git-Integration

### <a name="permissions"></a>Berechtigungen

Normalerweise möchten Sie nicht, dass jedes Teammitglied die Berechtigung zum Aktualisieren der Data Factory hat. Die folgenden Berechtigungseinstellungen werden empfohlen:

*   Alle Teammitglieder sollten über Leseberechtigungen für die Data Factory verfügen.
*   Nur eine ausgewählte Gruppe von Personen sollte in der Data Factory veröffentlichen dürfen. Zu diesem Zweck muss diesen Personen die Rolle **Data Factory-Mitwirkender** für die **Ressourcengruppe** zugewiesen sein, die die Data Factory enthält. Weitere Informationen zu Berechtigungen finden Sie unter [Rollen und Berechtigungen für Azure Data Factory](concepts-roles-permissions.md).

Es wird empfohlen, keine direkten Eincheckvorgänge im Kollaborationsbranch zuzulassen. Diese Einschränkung kann dazu beitragen, Fehler zu vermeiden, da jeder Eincheckvorgang einen Pull Request-Prozess durchläuft, der unter [Erstellen von Featurebranches](source-control.md#creating-feature-branches) beschrieben ist.

### <a name="using-passwords-from-azure-key-vault"></a>Verwenden von Kennwörtern aus Azure Key Vault

Es wird empfohlen, Azure Key Vault zum Speichern von Verbindungszeichenfolgen oder Kennwörtern oder zur Authentifizierung verwalteter Identitäten für verknüpfte Data Factory-Dienste zu verwenden. Aus Sicherheitsgründen speichert Data Factory Geheimnisse nicht in Git. Alle Änderungen an verknüpften Diensten mit Geheimnissen wie Kennwörtern werden sofort im Azure Data Factory-Dienst veröffentlicht.

Durch die Verwendung von Key Vault oder der MSI-Authentifizierung werden Continuous Integration und Continuous Deployment vereinfacht, da Sie diese Geheimnisse nicht bei der Bereitstellung von Resource Manager-Vorlagen angeben müssen.

## <a name="troubleshooting-git-integration"></a>Problembehandlung bei der Git-Integration

### <a name="stale-publish-branch"></a>Veralteter Branch für die Veröffentlichung

Wenn der Branch für die Veröffentlichung nicht mit dem Masterbranch synchron ist und trotz einer aktuellen Veröffentlichung veraltete Ressourcen enthält, versuchen Sie die folgenden Schritte:

1. Entfernen des aktuellen Git-Repositorys
1. Konfigurieren Sie Git mit denselben Einstellungen neu. Stellen Sie jedoch sicher, dass **Vorhandene Data Factory Ressourcen in Repository importieren** ausgewählt ist, und wählen Sie **Neuer Branch** aus.
1. Erstellen eines Pull Request zum Mergen der Änderungen in den Kollaborationsbranch 

Im Folgenden werden einige Beispielsituationen veranschaulicht, die zu einem veralteten Branch für die Veröffentlichung führen können:
- Ein Benutzer verfügt über mehrere Branches. In einem Featurebranch hat er einen verknüpften Dienst gelöscht, der nicht mit Azure Key Vault verbunden ist (nicht mit Azure Key Vault verknüpfte Dienste werden sofort veröffentlicht, unabhängig davon, ob sie sich in Git befinden). Der Featurebranch wurde nicht mit dem Zusammenarbeitsbranch zusammengeführt.
- Ein Benutzer hat die Data Factory über das SDK oder über PowerShell bearbeitet.
- Ein Benutzer hat alle Ressourcen zu einem neuen Branch migriert und zum ersten Mal versucht, eine Veröffentlichung durchzuführen. Verknüpfte Dienste sollten beim Importieren von Ressourcen manuell erstellt werden.
- Ein Benutzer lädt einen nicht mit Azure Key Vault verknüpften Dienst oder eine Integration Runtime-JSON-Datei manuell hoch. Er verweist aus einer anderen Ressource auf diese Ressource, z. B. in einem Dataset, einem verknüpften Dienst oder einer Pipeline. Ein über die Benutzeroberfläche erstellter, nicht mit Azure Key Vault verknüpfter Dienst wird sofort veröffentlicht, da die Anmeldeinformationen verschlüsselt werden müssen. Wenn Sie ein Dataset hochladen, das auf diesen verknüpften Dienst verweist, und versuchen, dieses zu veröffentlichen, wird das von der Benutzeroberfläche zugelassen, weil es in der Git-Umgebung enthalten ist. Es wird zum Zeitpunkt der Veröffentlichung abgelehnt, da es nicht im Data Factory-Dienst vorhanden ist.

## <a name="switch-to-a-different-git-repository"></a>Wechseln zu einem anderen Git-Repository

Wenn Sie zu einem anderen Git-Repository wechseln möchten, navigieren Sie im Verwaltungshub unter **Quellcodeverwaltung** zur Seite mit der Git-Konfiguration. Wählen Sie **Trennen** aus. 

![Git-Symbol](media/author-visually/remove-repository.png)

Geben Sie Ihren Data Factory Namen ein, und klicken Sie auf **Bestätigen**, um das Git-Repository zu entfernen, das Ihrer Data Factory-Instanz zugeordnet ist.

![Entfernen der Zuordnung zum aktuellen Git-Repository](media/author-visually/remove-repository-2.png)

Nachdem Sie die Zuordnung zum aktuellen Repository entfernt haben, können Sie Ihre Git-Einstellungen zur Verwendung eines anderen Repositorys konfigurieren und dann vorhandene Data Factory-Ressourcen in das neue Repository importieren.

> [!IMPORTANT]
> Beim Entfernen einer Git-Konfiguration aus einer Data Factory wird nichts aus dem Repository gelöscht. Die Factory enthält alle veröffentlichten Ressourcen. Sie können die Factory weiterhin direkt für den Dienst bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

* Unter [Programmgesteuertes Überwachen und Verwalten von Pipelines](monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines.
* Informationen zum Implementieren von Continuous Integration und Deployment finden Sie unter [Continuous Integration und Delivery (CI/CD) in Azure Data Factory](continuous-integration-deployment.md).
