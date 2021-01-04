---
title: Einrichten von GitHub Enterprise Server in Ihrer privaten Azure VMware Solution-Cloud
description: Erfahren Sie, wie Sie GitHub Enterprise Server in Ihrer privaten Azure VMware Solution-Cloud einrichten.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 00b3acf721dd7f7a1a15bcd0d24eccf3ca27ff58
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326912"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Einrichten von GitHub Enterprise Server in Ihrer privaten Azure VMware Solution-Cloud

In diesem Artikel werden die Schritte zum Einrichten von GitHub Enterprise Server, der "lokalen" Version von [GitHub.com](https://github.com/), in Ihrer privaten Azure VMware Solution-Cloud erläutert. Das in dieser exemplarischen Vorgehensweise beschriebene Szenario gilt für eine GitHub Enterprise Server-Instanz, die bis zu 3.000 Entwickler unterstützt, die bis zu 25 Einzelvorgänge pro Minute in GitHub Actions ausführen können. Dazu gehört die Einrichtung von Features wie GitHub Actions (bei der Erstellung dieses Dokuments lediglich als *Vorschau* verfügbar). Informationen zum Anpassen der Einrichtung an Ihre speziellen Anforderungen finden Sie in den Anforderungen zum [Installieren von GitHub Enterprise Server unter VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Vorbereitung

Für GitHub Enterprise Server ist ein gültiger Lizenzschlüssel erforderlich. Sie können sich für eine [Testlizenz](https://enterprise.github.com/trial) registrieren. Wenn Sie die Funktionen von GitHub Enterprise Server über eine Integration erweitern möchten, können Sie eine kostenlose Entwicklerlizenz für fünf Arbeitsplätze beantragen. Sie können sich im Rahmen des [GitHub-Partnerprogramms](https://partner.github.com/) für diese Lizenz bewerben.

## <a name="installing-github-enterprise-server-on-vmware"></a>Installieren von GitHub Enterprise Server unter VMware

Laden Sie [die aktuelle Version von GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) für VMware ESXi/vSphere (OVA) herunter, und [stellen Sie die heruntergeladene OVA-Vorlage bereit](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html).

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Auswahl, ob GitHub lokal oder in der Cloud ausgeführt werden soll.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Bereitstellen der OVA-Vorlage.":::  

Geben Sie einen erkennbaren Namen für den neuen virtuellen Computer an, z. B. GitHubEnterpriseServer. Sie müssen keine Versionsdetails in den VM-Namen einfügen, da diese Details beim Upgrade der Instanz belanglos werden. Wählen Sie vorerst alle Standardwerte aus (wir werden diese Details in Kürze bearbeiten), und warten Sie, bis die OVA importiert wurde.

[Passen Sie die Hardwarekonfiguration](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) nach dem Import Ihren Anforderungen entsprechend an. In unserem Beispielszenario wird die folgende Konfiguration benötigt.

| Resource | Standardeinrichtung | Standardeinrichtung + "Beta-Features" (GitHub Actions) |
| --- | --- | --- |
| vCPUs | 4 | 8 |
| Arbeitsspeicher | 32 GB | 61 GB |
| Angeschlossener Speicher | 250 GB | 300 GB |
| Stammspeicher | 200 GB | 200 GB |

Ihre jeweiligen Anforderungen können jedoch abweichen. Weitere Informationen finden Sie in den Überlegungen zur Hardware bei der [Installation von GitHub Enterprise Server unter VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Informationen zum Anpassen der Hardwarekonfiguration abhängig von der jeweiligen Situation finden Sie in der Beschreibung zum [Hinzufügen von CPU- oder Arbeitsspeicherressourcen für VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware).

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurieren der GitHub Enterprise Server-Instanz

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installieren Sie GitHub Enterprise.":::  

[Konfigurieren](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance) Sie den neu bereitgestellten virtuellen Computer (VM) nach dem Einschalten in Ihrem Browser. Sie müssen Ihre Lizenzdatei hochladen und ein Kennwort für die Verwaltungskonsole festlegen. Sie sollten dieses Kennwort unbedingt notieren und an einem sicheren Ort aufbewahren.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Zugriff auf die Verwaltungsshell über SSH.":::    

Es wird empfohlen, zumindest die folgenden Schritte auszuführen:

1. Laden Sie einen öffentlichen SSH-Schlüssel in die Verwaltungskonsole hoch, damit Sie [über SSH auf die Verwaltungsshell zugreifen](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) können. 

2. [Konfigurieren Sie TLS für Ihre Instanz](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls), damit Sie ein Zertifikat verwenden können, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Konfigurieren der Instanz.":::

Wenden Sie Ihre Einstellungen an.  Wenn die Instanz neu gestartet wird, können Sie mit dem nächsten Schritt, **Konfigurieren von Blob Storage für GitHub Actions**, fortfahren.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Erstellen des Administratorkontos.":::

Erstellen Sie ein neues Administratorkonto für die Instanz, nachdem die Instanz neu gestartet wurde. Sie sollten das Kennwort dieses Benutzers ebenfalls unbedingt notieren und sicher aufbewahren.

### <a name="additional-configuration-steps"></a>Zusätzliche Konfigurationsschritte

Zum Schützen Ihrer Instanz für die Verwendung in der Produktion werden die folgenden optionalen Einrichtungsschritte empfohlen:

1. Konfigurieren Sie die [Hochverfügbarkeit](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) zum Schutz vor:

    - Softwareabstürzen (Betriebssystem- oder Anwendungsebene)
    - Hardwarefehlern (Speicher, CPU, RAM usw.)
    - Fehlern im Virtualisierungshostsystem
    - Trennung vom logischen oder physischen Netzwerk

2. [Konfigurieren](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) Sie [Sicherungsdienstprogramme](https://github.com/github/backup-utils), die versionierte Momentaufnahmen für die Notfallwiederherstellung bereitstellen, die getrennt von der primären Instanz verfügbar gehostet werden.
3. Richten Sie die [Isolation von Unterdomänen](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation) unter Verwendung eines gültigen TLS-Zertifikats ein, um Cross-Site Scripting und andere damit verbundene Sicherheitsrisiken zu minimieren.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurieren von Blob Storage für GitHub Actions

> [!NOTE]
> GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).

Um GitHub Actions in GitHub Enterprise Server (derzeit als "Beta"-Feature verfügbar) zu aktivieren, ist ein externer Blobspeicher erforderlich. Dieser externe Blobspeicher wird von GitHub Actions zum Speichern von Artefakten und Protokollen verwendet. GitHub Actions in GitHub Enterprise Server unterstützt u. a. [Azure Blob Storage als Speicheranbieter](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements). Wir stellen nun ein neues Azure-Speicherkonto mit dem [Speicherkontotyp](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage bereit:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Bereitstellen eines Azure Blob Storage-Kontos.":::

Nachdem die neue BlobStorage-Ressource bereitgestellt wurde, kopieren Sie die Verbindungszeichenfolge (verfügbar unter den Zugriffsschlüsseln), und notieren Sie diese. Wir benötigen diese Zeichenfolge in Kürze.

## <a name="setting-up-the-github-actions-runner"></a>Einrichten des GitHub Actions-Runners

> [!NOTE]
> GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).

An diesem Punkt sollten Sie über eine aktive Instanz von GitHub Enterprise Server verfügen, für die ein Administratorkonto erstellt wurde. Darüber hinaus sollten Sie auch über externen Blobspeicher verfügen, den GitHub Actions für Speicherzwecke verwendet.

Lassen Sie uns nun einen Computer erstellen, in dem GitHub Actions ausgeführt wird. Auch hier verwenden wir Azure VMware Solution.

Lassen Sie uns zunächst einen neuen virtuellen Computer im Cluster bereitstellen. Als Basis für den virtuellen Computer verwenden wir [eine aktuelle Version von Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Bereitstellen einer neuen VM.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Bereitstellen einer neuen VM – Schritt 2.":::

Schalten Sie den virtuellen Computer ein, nachdem er erstellt wurde, und stellen Sie über SSH eine Verbindung her.

Installieren Sie als nächstes die [Actions-Runner](https://github.com/actions/runner)-Anwendung, die einen Einzelvorgang in einem GitHub Actions-Workflow ausführt. Ermitteln Sie die neueste Version der Linux x64-Version des Actions-Runners (entweder auf [der Seite mit den Versionen](https://github.com/actions/runner/releases) oder durch Ausführen des folgenden Kurzskripts) und laden Sie diese Version herunter. Für dieses Skript müssen sowohl curl als auch [jq](https://stedolan.github.io/jq/) in der VM vorhanden sein.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Sie sollten jetzt über eine lokale Datei in Ihrer VM verfügen, "actions-runner-linux-arm64-\*.tar.gz". Extrahieren Sie diese TAR-Datei lokal:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Bei dieser Extraktion werden einige Dateien lokal entpackt, einschließlich eines `config.sh`- und eines `run.sh`-Skripts, auf die wir in Kürze zurückkommen.

## <a name="enabling-github-actions"></a>Aktivieren von GitHub Actions

> [!NOTE]
> GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).

Wir haben es fast geschafft! Lassen Sie uns GitHub Actions in der GitHub Enterprise Server-Instanz konfigurieren und aktivieren. Dazu müssen wir [über SSH auf die Verwaltungsshell der GitHub Enterprise Server-Instanz zugreifen](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) und dann die folgenden Befehle ausführen:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Nächste Ausführung:

`ghe-actions-check -s blob`

Die folgende Ausgabe sollte angezeigt werden: "Blob Storage is healthy".

Nachdem GitHub Actions konfiguriert wurde, können Sie GitHub Actions für die Benutzer aktivieren. Melden Sie sich bei ihrer GitHub Enterprise Server-Instanz als Administrator an, und wählen Sie das ![Raketen-Symbol](media/github-enterprise-server/rocket-icon.png) aus, das in der rechten oberen Ecke jeder Seite verfügbar ist. Wählen Sie in der linken Randleiste **Enterprise overview**, **Policies**, **Actions** und dann die Option zum **Aktivieren von GitHub Actions für alle Organisationen** aus.

Konfigurieren Sie als nächstes den Runner auf der Registerkarte **Self-hosted runners**. Wählen Sie **Add new** und dann in der Dropdown-Liste **New runner** aus.

Auf der nächsten Seite werden mehrere Befehle angezeigt, die ausgeführt werden müssen. Wir müssen lediglich den Befehl zum **Konfigurieren** des Runners kopieren, z. B.:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Kopieren Sie den `config.sh`-Befehl, und fügen Sie ihn in eine Sitzung im (zuvor erstellten) Actions-Runner ein.

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Actions-Runner.":::

Verwenden Sie den run.sh-Befehl, um den Runner *auszuführen*:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Ausführen des Runners.":::

Um diesen Runner für Organisationen in Ihrem Unternehmen verfügbar zu machen, bearbeiten Sie den entsprechenden Zugriff für Organisationen:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Bearbeiten des Runner-Zugriffs.":::

Hier machen wir den Runner für alle Organisationen verfügbar. Sie können den Zugriff aber auch auf eine Teilmenge von Organisationen und sogar auf bestimmte Repositorys beschränken.

## <a name="optional-configuring-github-connect"></a>(Optional) Konfigurieren von GitHub Connect

Obwohl dieser Schritt optional ist, wird er empfohlen, wenn Sie beabsichtigen, auf GitHub.com verfügbare Open Source-Aktionen zu nutzen. Auf diese Weise können Sie auf der Arbeit anderer Benutzer aufbauen, indem Sie in Ihren Workflows auf diese wiederverwendbaren Aktionen verweisen.

Führen Sie die Schritte in den Anweisungen zum [Aktivieren des automatischen Zugriffs auf GitHub.com-Aktionen mit GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect) aus, um GitHub Connect zu aktivieren.

Wählen Sie die Option **Server to use actions from GitHub.com in workflow runs** aus, nachdem GitHub Connect aktiviert wurde.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Aktivieren der Verwendung von GitHub.com-Aktionen in ausgeführten Workflows.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Einrichten und Ausführen Ihres ersten Workflows

Nachdem GitHub Actions und GitHub Connect eingerichtet wurden, können wir all unsere Mühen vorteilhaft nutzen. Nachstehend sehen Sie ein Beispiel für einen Workflow, der auf [octokit/request-action](https://github.com/octokit/request-action) verweist, sodass wir GitHub über Interaktionen mithilfe der GitHub-API und Unterstützung von GitHub Actions "skriptgesteuert" verwenden können.

In diesem einfachen Workflow verwenden wir `octokit/request-action`, um lediglich ein GitHub-Issue mithilfe der API zu öffnen.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Beispielworkflow.":::

>[!NOTE]
>GitHub.com hostet die Aktion. Wird diese Aktion allerdings auf GitHub Enterprise Server ausgeführt, wird *automatisch* die GitHub Enterprise Server-API verwendet.

Wenn Sie GitHub Connect nicht aktivieren möchten, können Sie den folgenden alternativen Workflow verwenden.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternativer Beispielworkflow.":::

Navigieren Sie zu einem Repository in der Instanz, und fügen Sie den obigen Workflow als `.github/workflows/hello-world.yml` hinzu.

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Ein anderer Beispielworkflow.":::

Warten Sie auf der Registerkarte **Actions** für Ihr Repository, bis der Workflow ausgeführt wird.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Ausgeführter Beispielworkflow.":::

Sie können auch die Verarbeitung durch den Runner beobachten.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Workflowverarbeitung durch den Runner.":::

Wenn alles erfolgreich ausgeführt wurde, sollte in Ihrem Repository ein neues Issue mit dem Titel "Hello World" angezeigt werden.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Beispiel im Repository.":::

Herzlichen Glückwunsch! Sie haben soeben ihren ersten Actions-Workflow in GitHub Enterprise Server ausgeführt, der in Ihrer privaten Azure VMware Solution-Cloud ausgeführt wird.

Wir haben nur einen geringen Teil der Möglichkeiten aufgezeigt, die mit GitHub Actions durchgeführt werden können. Weitere Anregungen finden Sie in der Liste der Aktionen im [Marketplace von GitHub](https://github.com/marketplace), oder [erstellen Sie eigene Aktionen](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir eine neue GitHub Enterprise Server-Instanz, das selbstgehostete Äquivalent von GitHub.com, in ihrer privaten Azure VMware Solution-Cloud eingerichtet. Diese Instanz bietet Unterstützung für GitHub Actions und verwendet Azure Blob Storage zum Speichern von Protokollen und Artefakten. Diese Kombination ist für eine moderne, gemeinschaftliche und sichere Softwareentwicklungsumgebung hervorragend geeignet. Sie basiert auf Azure VMware Solution als solides Fundament, sodass Sie Cloudressourcen in einer vertrauten Umgebung nutzen können.

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Erste Schritte mit GitHub Actions](https://docs.github.com/en/actions)
- [Teilnahme am Betaprogramm](https://resources.github.com/beta-signup/)
- [Weitere Informationen zur Verwaltung von GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)