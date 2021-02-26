---
title: 'Vorgehensweise: Ausführen einer Anwendung mit Fortanix Confidential Computing Manager'
description: Hier erfahren Sie, wie Sie Fortanix Confidential Computing Manager zum Konvertieren Ihrer containerisierten Images verwenden.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 41e47e29553b3e4d9cd0e0e8f280b3648f224bf9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832305"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Anleitung: Ausführen einer Anwendung mit Fortanix Confidential Computing Manager

Beginnen Sie mit der Ausführung Ihrer Anwendung in Azure Confidential Computing mithilfe von [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) und [Fortanix Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) von [Fortanix](https://www.fortanix.com/).


Fortanix ist ein Drittanbieter von Software mit Produkten und Diensten, die auf Azure-Infrastruktur aufsetzen. Andere Drittanbieter bieten ähnliche Confidential Computing-Dienste in Azure an.

> [!Note]
 > Die Produkte, auf die in diesem Dokument verwiesen wird, unterliegen nicht der Kontrolle von Microsoft. Microsoft stellt Ihnen diese Informationen nur aus praktischen Gründen zur Verfügung, und der Verweis auf diese Nicht-Microsoft-Produkte bedeutet nicht, dass Microsoft sie empfiehlt.



In diesem Tutorial erfahren Sie, wie Sie Ihr Anwendungsimage in ein durch Confidential Computing geschütztes Image konvertieren können. In dieser Umgebung wird [Fortanix](https://www.fortanix.com/)-Software verwendet, die auf Intel SGX-fähigen virtuellen Computern der DCsv2-Serie von Azure läuft. Diese Lösung orchestriert kritische Sicherheitsrichtlinien wie Identitätsprüfung und Datenzugriffssteuerung.

 Um spezifische Unterstützung für Fortanix zu erhalten, treten Sie der [Fortanix Slack-Community](https://fortanix.com/community/) bei, und nutzen Sie den Kanal #enclavemanager.


## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie noch nicht über ein Fortanix Confidential Computing Manager-Konto verfügen, [registrieren Sie sich](https://em.fortanix.com/auth/sign-up), ehe Sie beginnen.
1. Eine private [Docker](https://docs.docker.com/)-Registrierung für die Pushübertragung konvertierter Anwendungsimages.
1. Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer möglich, die in diesem Tutorial verwendet werden. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Hinzufügen einer Anwendung zu Fortanix Confidential Computing Manager
1. Melden Sie sich bei [Fortanix Confidential Computing Manager (Fortanix EM)](https://em.fortanix.com) an.
1. Navigieren Sie zur Seite **Accounts** (Konten), und wählen Sie **ADD ACCOUNT** (Konto hinzufügen) aus, um ein neues Konto zu erstellen.

![Erstellen eines Kontos](media/how-to-fortanix-enclave-manager/create-account.png)

1. Nachdem Sie Ihr Konto erstellt haben, klicken Sie auf **SELECT** (Auswählen), um das neu erstellte Konto auszuwählen. Jetzt können wir mit dem Registrieren der Computeknoten und Erstellen von Anwendungen beginnen.
1. Wählen Sie zum Hinzufügen einer Anwendung die Schaltfläche **+ APPLICATION** (Anwendung hinzufügen) aus. In diesem Beispiel fügen wir eine Flask Server Enclave OS-Anwendung hinzu.

1. Wählen Sie die Schaltfläche **ADD** (Hinzufügen) aus, um die Enclave OS-Anwendung hinzuzufügen.

    ![Anwendung hinzufügen](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > In diesem Tutorial wird nur das Hinzufügen von Enclave OS-Anwendungen erklärt. [Informieren Sie sich weiter](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) über das Einbinden von EDP Rust-Anwendungen in Fortanix Confidential Computing Manager.

6. In diesem Tutorial verwenden wir für die Beispielanwendung die Docker-Registrierung von Fortanix. Geben Sie die folgenden Informationen ein. Verwenden Sie Ihre private Docker-Registrierung, um das Ausgabe-Image beizubehalten.

    - **Anwendungsname**: Python Application Server
    - **Beschreibung**: Python Flask Server
    - **Name des Eingabe-Images**: fortanix/python-flask
    - **Name des Ausgabe-Images**: fortanx-private/python-flask-sgx
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Arbeitsspeichergröße**: 1 GB
    - **Threadanzahl**: 128

    *Optional*: Führen Sie die Anwendung aus.
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/python-flask

        Führen Sie den folgenden Befehl aus:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Fügen Sie ein Zertifikat hinzu. Geben Sie die folgenden Informationen ein, und wählen Sie dann **NEXT** (Weiter) aus:
    - **Domäne**: myapp.domain.dom
    - **Typ:** Dies ist ein von Confidential Computing Manager ausgestelltes Zertifikat.
    - **Schlüsselpfad**: /appkey.pem
    - **Schlüsseltyp**: RSA
    - **Zertifikatpfad**: /appcert.pem
    - **RSA-Schlüsselgröße**: 2048 Bits


## <a name="create-an-image"></a>Erstellen eines Image
Ein Fortanix CCM-Image ist ein Softwarerelease oder eine Version einer Anwendung. Jedes Image ist einem Enclave-Hash (MRENCLAVE) zugeordnet.
1. Geben Sie auf der Seite **Add Image** (Image hinzufügen) die **REGISTRY CREDENTIALS** (Registrierungsanmeldeinfos) für **Output image name** (Name des Ausgabe-Images) ein. Diese Anmeldeinfos werden für den Zugriff auf die private Docker-Registrierung verwendet, in die das Image per Push übertragen wird.

    ![Image erstellen](media/how-to-fortanix-enclave-manager/create-image.png)
1. Geben Sie das Imagetag an, und wählen Sie **Create** (Erstellen) aus.

    ![Tag hinzufügen](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Zulassungsliste für Domäne und Image
Eine Anwendung, deren Domäne zur Zulassungsliste hinzugefügt wird, erhält von Fortanix Confidential Computing Manager ein TLS-Zertifikat. In ähnlicher Weise versucht eine Anwendung, wenn sie über das konvertierte Image ausgeführt wird, Fortanix Confidential Computing Manager zu kontaktieren. Die Anwendung fordert dann ein TLS-Zertifikat an.

Wechseln Sie auf der linken Seite zur Registerkarte **Tasks** (Aufgaben), und genehmigen Sie die ausstehenden Anforderungen, um die Domäne und das Image zuzulassen.

## <a name="enroll-compute-node-agent-in-azure"></a>Registrieren eines Computeknoten-Agents in Azure

### <a name="generate-and-copy-join-token"></a>Generieren und Kopieren des Tokens für Beitritt
Erstellen Sie ein Token in Fortanix Confidential Computing Manager. Dieses Token ermöglicht einem Computeknoten in Azure, sich selbst zu authentifizieren. Sie müssen dieses Token an Ihren virtuellen Azure-Computer übergeben.
1. Wählen Sie in der Verwaltungskonsole die Schaltfläche **ENROLL NODE** (Knoten registrieren) aus.
1. Wählen Sie **GENERATE TOKEN** (Token generieren) aus, um das Token für Beitritt zu generieren. Kopieren Sie das Token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrieren von Knoten in Fortanix Node Agent in Azure Marketplace

Beim Erstellen eines Fortanix Node Agents werden in Ihrer Azure-Ressourcengruppe ein virtueller Computer, eine Netzwerkschnittstelle, ein virtuelles Netzwerk, eine Netzwerksicherheitsgruppe und eine öffentliche IP-Adressen bereitgestellt. Ihr Azure-Abonnement für den virtuellen Computer wird stündlich abgerechnet. Bevor Sie einen Fortanix Node Agent erstellen, lesen Sie die [Azure-Seite mit den Preisen für virtuelle Computer ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) für die DCsv2-Serie. Löschen Sie Azure-Ressourcen, wenn sie nicht verwendet werden.

1. Wechseln Sie zu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Geben Sie in die Suchleiste **Fortanix Confidential Computing Node Agent** ein. Wählen Sie die App aus, die im Suchfeld **Fortanix Confidential Computing Node Agent** angezeigt wird, um zur Startseite des Angebots zu navigieren.
     ![Marketplace durchsuchen](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Wählen Sie **Jetzt holen** aus. Geben Sie bei Bedarf Ihre Informationen ein, und wählen Sie **Weiter** aus. Sie werden zum Azure-Portal umgeleitet.
1. Wählen Sie **Erstellen** aus, um die Seite zur Bereitstellung des Fortanix Confidential Computing Node Agents aufzurufen.
1. Auf dieser Seite geben Sie Informationen zur Bereitstellung eines virtuellen Computers ein. Konkret handelt es sich bei dieser VM um einen Intel SGX-fähigen virtuellen Computer der DCsv2-Serie von Azure mit installierter Fortanix Node Agent-Software. Der Node Agent ermöglicht die sichere Ausführung Ihres konvertierten Images auf Intel SGX-Knoten in Azure.  Wählen Sie das  **Abonnement** und die  **Ressourcengruppe** aus, in der Sie den virtuellen Computer und die zugehörigen Ressourcen bereitstellen möchten.

    > [!NOTE]
    > Für die Bereitstellung von virtuellen Computern der DCsv2-Serie in Azure gelten Einschränkungen. Möglicherweise müssen Sie Kontingente für zusätzliche Kerne anfordern. Weitere Informationen finden Sie unter [Confidential Computing-Lösungen auf Azure VMs](./virtual-machine-solutions.md).

1. Wählen Sie eine verfügbare Region aus.
1. Geben Sie in das Feld **Knotenname** einen Namen für Ihren virtuellen Computer ein.
1. Geben Sie den Benutzernamen und das Kennwort (oder SSH-Schlüssel) zur Authentifizierung beim virtuellen Computer ein.
1. Belassen Sie die Standardgröße des Betriebssystem-Datenträgers bei 200, und wählen Sie eine VM-Größe aus (Standard_DC4s_v2 ist für dieses Tutorial ausreichend).
1. Fügen Sie das zuvor generierte Token in das Feld **Token für Beitritt** ein.

     ![Ressource bereitstellen](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent1.png)

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus. Sobald alle Ressourcen bereitgestellt sind, ist der Computeknoten in Fortanix Confidential Computing Manager registriert.

## <a name="run-the-application-image-on-the-compute-node"></a>Ausführen des Anwendungsimages auf dem Computeknoten
Führen Sie die Anwendung aus, indem Sie den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie „Node IP (IP-Adresse des Knoten), „Port“ und „Converted Image Name“ (Name des konvertierten Images) als Eingaben für die jeweilige Anwendung ändern.

In diesem Tutorial lautet der auszuführende Befehl wie folgt:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Erläuterungen:
- *52.152.206.164* ist die Host-IP-Adresse des Node Agents.
- *9092* ist der Port, an dem der Node Agent lauscht.
- *fortanix-private/python-flask-sgx* ist die konvertierte App, die im Fortanix Confidential Computing Manager-Webportal auf der Registerkarte „Images“ unter der Spalte **Imagename** in der Tabelle **Images** zu finden ist.

## <a name="verify-and-monitor-the-running-application"></a>Überprüfen und Überwachen der laufenden Anwendung
1. Navigieren Sie zurück zu [Fortanix Confidential Computing Manager](https://em.fortanix.com/console).
1. Stellen Sie sicher, dass Sie im **Konto** agieren, bei dem Sie den Knoten registriert haben.
1. Navigieren Sie zur **Management Console** (Verwaltungskonsole), indem Sie im linken Navigationsbereich das oberste Symbol auswählen.
1. Wählen Sie die Registerkarte **Application** (Anwendung) aus.
1. Bestätigen, dass es eine laufende Anwendung mit einem zugehörigen Computeknoten gibt.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, der virtuelle Computer und zugehörige Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Beim Löschen der Ressourcengruppe wird die Registrierung der Ihrem konvertierten Image zugeordneten Knoten aufgehoben.

Wählen Sie die Ressourcengruppe für den virtuellen Computer und dann **Löschen** aus. Bestätigen Sie dann den Namen der Ressourcengruppe, um das Löschen der Ressourcen abzuschließen.

Navigieren Sie zur [Seite „Konten“](https://em.fortanix.com/accounts) in Fortanix Confidential Computing Manager, um das erstellte Fortanix Confidential Computing Manager-Konto zu löschen. Bewegen Sie den Mauszeiger über dem Konto, das Sie löschen möchten. Wählen Sie rechts oben die vertikalen schwarzen Punkte und danach **Delete Account** (Konto löschen) aus.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie mit Fortanix-Tools Ihr Anwendungsimage so konvertiert, dass es auf einer Confidential Computing-VM läuft. Weitere Informationen zu virtuellen Confidential Computing-Computern in Azure finden Sie unter [Lösungen für virtuelle Computer](virtual-machine-solutions.md).

Weitere Informationen zu den Confidential Computing-Angeboten von Azure finden Sie unter [Azure Confidential Computing: Übersicht](overview.md).

 Erfahren Sie, wie Sie ähnliche Aufgaben mit anderen Drittanbieterangeboten für Azure wie [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) und [Scone](https://sconedocs.github.io) erledigen können.
