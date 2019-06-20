---
title: Verwenden von Azure Container Instances als Jenkins-Build-Agent
description: Hier erfahren Sie, wie Sie Azure Container Instances als Jenkins-Build-Agent verwenden.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 2687a64bfd952888086862d929a3e5869aee5a4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584036"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Verwenden von Azure Container Instances als Jenkins-Build-Agent

Mit Azure Container Instances (ACI) steht Ihnen eine bedarfsgesteuerte, burstfähige und isolierte Umgebung zum Ausführen von containerbasierten Workloads zur Verfügung. Aufgrund dieser Attribute ist ACI eine hervorragende Plattform für die Ausführung von Jenkins-Buildaufträgen in großem Umfang. In diesem Artikel werden ausführlich die Bereitstellung und Verwendung eines Jenkins-Servers erläutert, der mit ACI als Buildziel vorkonfiguriert wurde.

Weitere Informationen zu Azure Container Instances finden Sie unter [Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Bereitstellen eines Jenkins-Servers

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**, und suchen Sie nach **Jenkins**. Wählen Sie das Jenkins-Angebot mit dem Herausgeber **Microsoft** aus, und wählen Sie dann **Erstellen**.

2. Geben Sie im Formular **Grundlagen** die folgenden Informationen ein, und wählen Sie anschließend **OK**.

   - **Name**: Geben Sie einen Namen für die Jenkins-Bereitstellung ein.
   - **Benutzername**: Geben Sie einen Namen für den Administrator des virtuellen Jenkins-Computers ein.
   - **Authentifizierungstyp:** Die Verwendung eines öffentlichen SSH-Schlüssels für die Authentifizierung wird empfohlen. Wenn Sie diese Option wählen, fügen Sie einen öffentlichen SSH-Schlüssel ein, der für die Anmeldung am virtuellen Jenkins-Computer verwendet werden soll.
   - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
   - **Ressourcengruppe**: Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene aus.
   - **Standort**: Wählen Sie einen Standort für den Jenkins-Server aus.

   ![Grundlegende Einstellungen für die Jenkins-Bereitstellung im Portal](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Geben Sie im Formular **Zusätzliche Einstellungen** die folgenden Informationen ein:

   - **Größe**: Wählen Sie die geeignete Größenoption für den virtuellen Jenkins-Computer aus.
   - **VM-Datenträgertyp**: Geben Sie für den Jenkins-Server entweder **HDD** (Festplattenlaufwerk) oder **SSD** (Solid State Drive) an.
   - **Virtuelles Netzwerk:** Wählen Sie den Pfeil aus, wenn Sie die Standardeinstellungen ändern möchten.
   - **Subnetze**: Wählen Sie den Pfeil aus, überprüfen Sie die Informationen, und wählen Sie dann **OK** aus.
   - **Öffentliche IP-Adresse:** Wählen Sie den Pfeil aus, um der öffentlichen IP-Adresse einen benutzerdefinierten Namen zu geben, konfigurieren Sie die SKU, und legen Sie die Zuweisungsmethode fest.
   - **Domänennamenbezeichnung**: Geben Sie einen Wert an, um eine vollqualifizierte URL zum virtuellen Jenkins-Computer zu erstellen.
   - **Jenkins-Releasetyp**: Wählen Sie den gewünschten Releasetyp aus den Optionen aus: **LTS**, **Wöchentlicher Build** oder **Azure verifiziert**.

   ![Zusätzliche Einstellungen für die Jenkins-Bereitstellung im Portal](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Wählen Sie für die Dienstprinzipalintegration **Auto(MSI)** aus, damit [verwaltete Azure-Identitäten für Azure-Ressourcen][managed-identities-azure-resources] automatisch eine Authentifizierungsidentität für die Jenkins-Instanz erstellen. Wählen Sie **Manuell** aus, um Ihre eigenen Dienstprinzipal-Anmeldeinformationen anzugeben.

5. Cloud-Agents konfigurieren eine cloudbasierte Plattform für Jenkins-Buildaufträge. Wählen Sie in diesem Artikel **ACI** aus. Mit dem ACI-Cloud-Agent wird jeder Jenkins-Buildauftrag in einer Containerinstanz ausgeführt.

   ![Cloudintegrationseinstellungen für die Jenkins-Bereitstellung im Portal](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Nachdem Sie die Integrationseinstellungen angegeben haben, wählen Sie **OK** und anschließend in der Validierungszusammenfassung erneut **OK**. Wählen Sie in der Zusammenfassung der **Nutzungsbedingungen** die Option **Erstellen** aus. Die Bereitstellung des Jenkins-Servers dauert einige Minuten.

## <a name="configure-jenkins"></a>Konfigurieren von Jenkins

1. Navigieren Sie im Azure-Portal zur Jenkins-Ressourcengruppe, wählen Sie den virtuellen Jenkins-Computer aus, und notieren Sie sich den DNS-Namen.

   ![DNS-Name in den Details zum virtuellen Jenkins-Computer](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Navigieren Sie zum DNS-Namen des virtuellen Jenkins-Computers, und kopieren Sie die zurückgegebene SSH-Zeichenfolge.

   ![Jenkins-Anmeldeanweisungen mit SSH-Zeichenfolge](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Öffnen Sie eine Terminalsitzung auf Ihrem Bereitstellungssystem, und fügen Sie die SSH-Zeichenfolge aus dem letzten Schritt ein. Verwenden Sie für `username` den Benutzernamen, den Sie beim Bereitstellen des Jenkins-Servers festgelegt haben.

4. Führen Sie nach dem Verbinden der Sitzung den folgenden Befehl aus, um das anfängliche Administratorkennwort abzurufen:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Navigieren Sie in einem Browser zu `http://localhost:8080`, ohne die SSH-Sitzung und den Tunnel zu schließen. Fügen Sie das ursprüngliche Administratorkennwort in das Feld ein, und wählen Sie dann **Continue** (Fortfahren).

   ![Der Bildschirm „Entsperren von Jenkins“ mit dem Feld für das Administratorkennwort](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Klicken Sie auf **Install suggested plugins** (Vorgeschlagene Plug-Ins installieren), um alle empfohlenen Jenkins-Plug-Ins zu installieren.

   ![Der Bildschirm „Entsperren von Jenkins“ mit ausgewählter Option „Vorgeschlagene Plug-Ins installieren“](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Erstellen Sie ein Administratorbenutzerkonto. Dieses Konto wird zum Anmelden bei und Arbeiten mit der Jenkins-Instanz verwendet.

   ![Der Bildschirm „Ersten Administratorbenutzer erstellen“ mit ausgefüllten Anmeldeinformationen](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Wählen Sie **Save and Finish** (Speichern und beenden) und dann **Start using Jenkins** (Jenkins verwenden), um die Konfiguration abzuschließen.

Jenkins ist jetzt konfiguriert und kann zum Erstellen und Bereitstellen von Code verwendet werden. In diesem Beispiel wird anhand einer einfachen Java-Anwendung ein Jenkins-Build in Azure Container Instances veranschaulicht.

## <a name="create-a-build-job"></a>Erstellen eines Buildauftrags

Nun wird ein Jenkins-Buildauftrag erstellt, um Jenkins-Builds auf einer Azure-Containerinstanz zu veranschaulichen.

1. Klicken Sie auf **Neues Element**, geben Sie dem Buildprojekt einen Namen wie **aci-demo**, wählen Sie **Freestyle project** (Freestyleprojekt) aus, und klicken Sie dann auf **OK**.

   ![Das Feld für den Namen des Buildauftrags und die Liste der Projekttypen](./media/container-instances-jenkins/jenkins-new-job.png)

2. Vergewissern Sie sich, dass unter **General** (Allgemein) die Option **Restrict where this project can be run** (Ausführungsort dieses Projekts beschränken) aktiviert ist. Geben Sie als Bezeichnungsausdruck **linux** ein. Mit dieser Konfiguration wird sichergestellt, dass dieser Buildauftrag in der ACI-Cloud ausgeführt wird.

   ![Die Registerkarte „Allgemein“ mit Konfigurationsdetails](./media/container-instances-jenkins/jenkins-job-01.png)

3. Klicken Sie unter **Build** auf **Buildschritt hinzufügen**, und klicken Sie auf **Execute Shell** (Shell ausführen). Geben Sie den Befehl `echo "aci-demo"` ein.

   ![Die Registerkarte „Build“ mit der Auswahl für den Buildschritt](./media/container-instances-jenkins/jenkins-job-02.png)

5. Wählen Sie **Speichern** aus.

## <a name="run-the-build-job"></a>Ausführen des Buildauftrags

Starten Sie einen Build manuell, um den Buildauftrag zu testen und sich mit Azure Container Instances als Buildplattform vertraut zu machen.

1. Klicken Sie auf **Build Now** (Jetzt erstellen), um einen Buildauftrag zu starten. Es dauert einige Minuten, bis der Auftrag gestartet wird. Eine Status ähnlich der folgenden Abbildung wird angezeigt:

   ![Informationen mit dem Auftragsstatus „Buildverlauf“](./media/container-instances-jenkins/jenkins-job-status.png)

2. Öffnen Sie während der Auftragsausführung das Azure-Portal, und sehen Sie sich die Jenkins-Ressourcengruppe an. Sie sollten sehen, dass eine Containerinstanz erstellt wurde. Der Jenkins-Auftrag wird in dieser Instanz ausgeführt.

   ![Containerinstanz in der Ressourcengruppe](./media/container-instances-jenkins/jenkins-aci.png)

3. Da Jenkins mehr Aufträge als die konfigurierte Anzahl von Jenkins-Executors (Standard: 2) ausführt, werden mehrere Containerinstanzen erstellt.

   ![Neu erstellte Containerinstanzen](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Nach Abschluss aller Buildaufträge, werden die Containerinstanzen entfernt.

   ![Ressourcengruppe mit entfernten Containerinstanzen](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Jenkins in Azure finden Sie unter [Azure und Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md