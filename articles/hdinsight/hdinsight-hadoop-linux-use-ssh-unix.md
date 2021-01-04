---
title: Verwenden von SSH mit Hadoop – Azure HDInsight
description: Sie können auf HDInsight über Secure Shell (SSH) zugreifen. Dieses Dokument enthält Informationen zum Herstellen einer Verbindung mit HDInsight mithilfe des ssh-Befehls über Windows-, Linux-, Unix- oder macOS-Clients.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: dbbb205aea48dedbe13696d0a43e632faf2ae339
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546142"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH

Informieren Sie sich darüber, wie Sie [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) verwenden, um eine sichere Verbindung mit Apache Hadoop in Azure HDInsight herzustellen. Informationen zum Herstellen einer Verbindung über ein virtuelles Netzwerk finden Sie unter [Virtuelle Netzwerkarchitektur mit Azure HDInsight](./hdinsight-virtual-network-architecture.md). Weitere Informationen finden Sie unter [Planen eines virtuellen Netzwerks für Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

Die folgende Tabelle enthält die Adress- und Portinformationen, die zum Herstellen der Verbindung mit HDInsight über einen SSH-Client benötigt werden:

| Adresse | Port | Verbindungsherstellung mit... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Primärer Hauptknoten |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Sekundärer Hauptknoten |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Edgeknoten (ML Services in HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Edgeknoten (alle anderen Clustertypen, sofern ein Edgeknoten vorhanden ist) |

Ersetzen Sie `<clustername>` durch den Namen Ihres Clusters. Ersetzen Sie `<edgenodename>` durch den Namen des Edgeknotens.

Wenn Ihr Cluster einen Edgeknoten enthält, sollten Sie __stets die Verbindung mit dem Edgeknoten__ mithilfe von SSH herstellen. Auf den Hauptknoten werden Dienste gehostet, die für die Integrität von Hadoop wichtig sind. Auf dem Edgeknoten werden nur die Komponenten ausgeführt, die Sie darauf anordnen. Weitere Informationen zur Verwendung von Edgeknoten finden Sie unter [Verwenden leerer Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Wenn Sie zum ersten Mal eine Verbindung mit HDInsight herstellen, zeigt der SSH-Client unter Umständen eine Warnung mit dem Hinweis an, dass die Echtheit des Hosts nicht bestätigt werden kann. Klicken Sie in diesem Fall auf „Ja“, um den Host der Liste mit den vertrauenswürdigen Servern des SSH-Clients hinzuzufügen.
>
> Wenn Sie zuvor eine Verbindung mit einem Server mit dem gleichen Namen hergestellt haben, erscheint unter Umständen eine Warnung mit dem Hinweis, dass der gespeicherte Hostschlüssel nicht dem Hostschlüssel des Servers entspricht. Informationen zum Entfernen des Eintrags für den Servernamen finden Sie in der Dokumentation Ihres SSH-Clients.

## <a name="ssh-clients"></a>SSH-Clients

Linux-, Unix- und macOS-Systeme bieten die Befehle `ssh` und `scp`. Der `ssh`-Client wird häufig verwendet, um eine Remote-Befehlszeilensitzung mit einem Linux- oder Unix-basierten System zu erstellen. Der `scp`-Client wird verwendet, um Dateien zwischen Ihrem Client und dem Remotesystem sicher zu kopieren.

In Microsoft Windows werden standardmäßig keine SSH-Clients installiert. Die `ssh`- und `scp`-Clients sind für Windows über die folgenden Pakete verfügbar:

* [OpenSSH-Client](/windows-server/administration/openssh/openssh_install_firstuse). Dieser Client ist ein optionales Feature, das mit dem Windows 10 Fall Creators Update eingeführt wurde.

* [Bash auf Ubuntu unter Windows 10](/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell bietet eine Bash-Umgebung in Ihrem Browser.

* [Git](https://git-scm.com/).

Es gibt auch mehrere grafische SSH-Clients wie z. B. [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) und [MobaXterm](https://mobaxterm.mobatek.net/). Diese Clients können zum Herstellen einer Verbindung mit HDInsight verwendet werden, aber der Prozess der Verbindungsherstellung ist anders als bei der Nutzung des Hilfsprogramms `ssh`. Weitere Informationen finden Sie in der Dokumentation des grafischen Clients, den Sie verwenden.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Authentifizierung: SSH-Schlüssel

Für SSH-Schlüssel wird die [Verschlüsselung mit öffentlichen Schlüsseln](https://en.wikipedia.org/wiki/Public-key_cryptography) zum Authentifizieren von SSH-Sitzungen verwendet. SSH-Schlüssel sind sicherer als Kennwörter und stellen einen einfachen Weg zum Schützen des Zugriffs auf Ihren Hadoop-Cluster dar.

Wenn Ihr SSH-Konto mit einem Schlüssel geschützt ist, muss der Client beim Herstellen der Verbindung den passenden privaten Schlüssel bereitstellen:

* Die meisten Clients können für die Verwendung eines __Standardschlüssels__ konfiguriert werden. Beispielsweise sucht der `ssh`-Client in Linux- und Unix-Umgebungen unter `~/.ssh/id_rsa` nach einem privaten Schlüssel.

* Sie können den __Pfad zu einem privaten Schlüssel__ angeben. Beim `ssh`-Client wird der Parameter `-i` verwendet, um den Pfad zum privaten Schlüssel anzugeben. Beispiel: `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Wenn Sie __mehrere private Schlüssel__ mit verschiedenen Servern nutzen, können Sie Hilfsprogramme wie [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) verwenden. Das Hilfsprogramm `ssh-agent` kann verwendet werden, um automatisch den Schlüssel auszuwählen, der beim Herstellen einer SSH-Sitzung verwendet werden soll.

> [!IMPORTANT]  
> Falls Sie Ihren privaten Schlüssel mit einer Passphrase schützen, müssen Sie die Passphrase beim Verwenden des Schlüssels eingeben. Mit Hilfsprogrammen wie `ssh-agent` kann das Kennwort zwischengespeichert werden, um Ihnen den Vorgang zu erleichtern.

### <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Verwenden Sie den Befehl `ssh-keygen`, um Dateien mit öffentlichen und privaten Schlüsseln zu erstellen. Mit dem folgenden Befehl wird ein RSA-Schlüsselpaar (2048 Bit) generiert, das mit HDInsight verwendet werden kann:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Beim Erstellen des Schlüssels werden Sie zum Eingeben von Informationen aufgefordert. Dies können beispielsweise Informationen dazu sein, wo die Schlüssel gespeichert sind oder ob eine Passphrase verwendet werden soll. Nach Abschluss des Vorgangs werden zwei Dateien erstellt: ein öffentlicher Schlüssel und ein privater Schlüssel.

* Der __öffentliche Schlüssel__ wird zum Erstellen eines HDInsight-Clusters verwendet. Der öffentliche Schlüssel hat die Erweiterung `.pub`.

* Der __private Schlüssel__ wird verwendet, um Ihren Client im HDInsight-Cluster zu authentifizieren.

> [!IMPORTANT]  
> Sie können Ihre Schlüssel mit einer Passphrase schützen. Eine Passphrase ist praktisch ein Kennwort für Ihren privaten Schlüssel. Wenn Ihr privater Schlüssel in den Besitz einer anderen Person gelangt, muss diese Person für die Nutzung des Schlüssels dann auch über die Passphrase verfügen.

### <a name="create-hdinsight-using-the-public-key"></a>Erstellen von HDInsight-Clustern mit dem öffentlichen Schlüssel

| Erstellungsmethode | Verwenden des öffentlichen Schlüssels |
| ------- | ------- |
| Azure-Portal | Deaktivieren Sie __Verwenden Sie ein Clusteranmeldekennwort für SSH__ , und wählen Sie dann __Öffentlicher Schlüssel__ als SSH-Authentifizierungstyp aus. Wählen Sie abschließend die Datei mit dem öffentlichen Schlüssel aus, oder fügen Sie den Textinhalt der Datei im Feld __Öffentlicher SSH-Schlüssel__ ein.</br>![Dialogfeld „Öffentlicher SSH-Schlüssel“ bei der Erstellung des HDInsight-Clusters](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Verwenden Sie den Parameter `-SshPublicKey` des Cmdlets [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster), und übergeben Sie den Inhalt des öffentlichen Schlüssels als Zeichenfolge.|
| Azure CLI | Verwenden Sie den Parameter `--sshPublicKey` des Befehls [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create), und übergeben Sie den Inhalt des öffentlichen Schlüssels als Zeichenfolge. |
| Resource Manager-Vorlage | Ein Beispiel für die Verwendung von SSH-Schlüsseln mit einer Vorlage finden Sie unter [Deploy HDInsight on Linux (w/ Azure Storage, SSH key)](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Bereitstellen von HDInsight unter Linux (mit Azure Storage, SSH-Schlüssel)). Das `publicKeys`-Element in der Datei [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) wird zum Übergeben der Schlüssel an Azure während der Erstellung des Clusters verwendet. |

## <a name="authentication-password"></a>Authentifizierung: Kennwort

SSH-Konten können mit einem Kennwort geschützt werden. Beim Herstellen der Verbindung mit HDInsight per SSH werden Sie zum Eingeben des Kennworts aufgefordert.

> [!WARNING]  
> Microsoft rät davon ab, für SSH die Kennwortauthentifizierung zu verwenden. Kennwörter können erraten werden und sind anfällig für Brute-Force-Angriffe. Stattdessen empfehlen wir die Verwendung von [SSH-Schlüsseln für die Authentifizierung](#sshkey).

> [!IMPORTANT]  
> Das Kennwort für das SSH-Konto läuft 70 Tage nach Erstellung des HDInsight-Clusters ab. Wenn Ihr Kennwort abgelaufen ist, können Sie es anhand der Informationen im Dokument [Verwalten von HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) ändern.

### <a name="create-hdinsight-using-a-password"></a>Erstellen eines HDInsight-Clusters mit einem Kennwort

| Erstellungsmethode | Angeben des Kennworts |
| --------------- | ---------------- |
| Azure-Portal | Standardmäßig gilt für das SSH-Benutzerkonto dasselbe Kennwort wie für das Anmeldekonto für den Cluster. Sie können ein anderes Kennwort verwenden, indem Sie die Option __Verwenden Sie ein Clusteranmeldekennwort für SSH__ deaktivieren und das Kennwort im Feld __SSH-Kennwort__ eingeben.</br>![Dialogfeld „SSH-Kennwort“ bei der Erstellung des HDInsight-Clusters](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Verwenden Sie den Parameter `--SshCredential` des Cmdlets [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster), und übergeben Sie ein `PSCredential`-Objekt, in dem der Name und das Kennwort des SSH-Benutzerkontos enthalten sind. |
| Azure CLI | Verwenden Sie den Parameter `--ssh-password` des Befehls [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create), und geben Sie den Kennwortwert an. |
| Resource Manager-Vorlage | Ein Beispiel für die Verwendung eines Kennworts mit einer Vorlage finden Sie unter [Deploy HDInsight cluster with Storage and SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Bereitstellen eines HDInsight-Clusters mit Storage und SSH-Kennwort). Das `linuxOperatingSystemProfile`-Element in der Datei [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) wird zum Übergeben des Namens und Kennworts für das SSH-Konto an Azure während der Erstellung des Clusters verwendet.|

### <a name="change-the-ssh-password"></a>Ändern des SSH-Kennworts

Informationen zum Ändern des Kennworts für das SSH-Benutzerkonto finden Sie im Abschnitt __Ändern von Kennwörtern__ des Dokuments [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="authentication-domain-joined-hdinsight"></a>In die Authentifizierungsdomäne eingebundenes HDInsight

Wenn Sie einen __in die Domäne eingebundenen HDInsight-Cluster__ verwenden, müssen Sie nach dem Herstellen der Verbindung mit dem lokalen SSH-Benutzer den Befehl `kinit` verwenden. Bei diesem Befehl werden Sie zum Eingeben eines Benutzers und Kennworts für die Domäne aufgefordert, und Ihre Sitzung wird anhand der Azure Active Directory-Domäne authentifiziert, die dem Cluster zugeordnet ist.

Sie können auch auf jedem in die Domäne eingebundenen Knoten (z. B. Hauptknoten, Edgeknoten) die Kerberos-Authentifizierung aktivieren, um mit dem Domänenkonto eine SSH-Verbindung herzustellen. Bearbeiten Sie hierzu die sshd-Konfigurationsdatei:

```bash
sudo vi /etc/ssh/sshd_config
```

Auskommentierung aufheben und `KerberosAuthentication` in `yes` ändern

```bash
sudo service sshd restart
```

Mit dem `klist`-Befehl können Sie überprüfen, ob die Kerberos-Authentifizierung erfolgreich war.

Weitere Informationen finden Sie unter [Configure Domain-joined HDInsight clusters (Preview)](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) (Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern (Vorschau)).

## <a name="connect-to-nodes"></a>Herstellen einer Verbindung mit Knoten

Auf die Hauptknoten und den Edgeknoten (sofern vorhanden) kann über das Internet an den Ports 22 und 23 zugegriffen werden.

* Verwenden Sie beim Herstellen einer Verbindung mit den __Hauptknoten__ den Port __22__ , um eine Verbindung mit dem primären Hauptknoten herzustellen, und den Port __23__ , um eine Verbindung mit dem sekundären Hauptknoten herzustellen. Der zu verwendende vollqualifizierte Domänennamen lautet `clustername-ssh.azurehdinsight.net`, wobei `clustername` der Name Ihres Clusters ist.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Verwenden Sie beim Herstellen einer Verbindung mit dem __Edgeknoten__ den Port 22. Der zu verwendende vollqualifizierte Domänennamen lautet `edgenodename.clustername-ssh.azurehdinsight.net`, wobei `edgenodename` der Name ist, den Sie beim Erstellen des Edgeknotens angegeben haben. `clustername` ist der Name des Clusters.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> In den vorherigen Beispielen wird davon ausgegangen, dass Sie die Kennwortauthentifizierung verwenden oder dass die Zertifikatauthentifizierung automatisch stattfindet. Wenn Sie ein SSH-Schlüsselpaar für die Authentifizierung verwenden und das Zertifikat nicht automatisch verwendet wird, geben Sie mithilfe des Parameters `-i` den privaten Schlüssel an. Beispiel: `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Nachdem die Verbindung hergestellt wurde, gibt die Eingabeaufforderung den SSH-Benutzernamen und den Knoten an, mit dem die Verbindung besteht. Wenn Sie also beispielsweise als `sshuser` mit dem primären Hauptknoten verbunden sind, lautet die Eingabeaufforderung `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Herstellen einer Verbindung mit Worker- und Apache ZooKeeper-Knoten

Auf die Worker- und Zookeeper-Knoten kann nicht direkt über das Internet zugegriffen werden. Auf sie kann über Clusterhauptknoten oder über Edgeknoten zugegriffen werden. Im Anschluss finden Sie die allgemeinen Schritte zum Verbinden mit anderen Knoten:

1. Herstellen einer SSH-Verbindung mit einem Haupt- oder Edgeknoten:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Verwenden Sie für die SSH-Verbindung mit dem Haupt- oder Edgeknoten den Befehl `ssh`, um eine Verbindung mit einem Workerknoten im Cluster herzustellen:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Informationen zum Abrufen der Liste mit den Knotennamen finden Sie im Dokument [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

Wenn das SSH-Konto mit einem __Kennwort__ gesichert wird, geben Sie das Kennwort ein, wenn Sie eine Verbindung herstellen.

Wenn das SSH-Konto mit __SSH-Schlüsseln__ gesichert wird, stellen Sie sicher, dass die SSH-Weiterleitung auf dem Client aktiviert ist.

> [!NOTE]  
> Eine weitere Möglichkeit zum direkten Zugreifen auf alle Knoten im Cluster ist die Installation von HDInsight in einem Azure Virtual Network. Anschließend können Sie Ihren Remotecomputer mit demselben virtuellen Netzwerk verknüpfen und auf alle Knoten im Cluster direkt zugreifen.
>
> Weitere Informationen finden Sie unter [Planen eines virtuellen Netzwerks für HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurieren der SSH-Agent-Weiterleitung

> [!IMPORTANT]  
> In den folgenden Schritten wird davon ausgegangen, dass Sie ein Linux- oder UNIX-basiertes System und Bash unter Windows 10 verwenden. Falls diese Schritte für Ihr System nicht geeignet sind, ziehen Sie die Dokumentation für Ihren SSH-Client zurate.

1. Öffnen Sie `~/.ssh/config`in einem Text-Editor. Sollte die Datei nicht vorhanden sein, können Sie sie durch Eingabe von `touch ~/.ssh/config` an einer Befehlszeile erstellen.

2. Fügen Sie der Datei `config` den folgenden Text hinzu:

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Ersetzen Sie die __Host__ -Informationen durch die Adresse des Knotens, mit dem Sie per SSH eine Verbindung herstellen. Im vorherigen Beispiel wird der Edgeknoten verwendet. Mit diesem Eintrag wird die SSH-Agent-Weiterleitung für den angegebenen Knoten konfiguriert.

3. Testen Sie die SSH-Agent-Weiterleitung über die Eingabe des folgenden Befehls in das Terminal:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Die Ausgabe dieses Befehls sieht in etwa wie folgt aus:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Sollte nichts zurückgegeben werden, wird `ssh-agent` nicht ausgeführt. Weitere Informationen finden Sie in den Informationen zu den Agent-Startskripts unter [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Verwenden von „ssh-agent“ mit SSH) oder in der Dokumentation Ihres SSH-Clients.

4. Nachdem Sie sichergestellt haben, dass **ssh-Agent** ausgeführt wird, verwenden Sie folgenden Befehl, um Ihren privaten SSH-Schlüssel dem Agent hinzuzufügen:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Wenn Ihr privater Schlüssel in einer anderen Datei gespeichert ist, ersetzen Sie `~/.ssh/id_rsa` durch den Pfad zur Datei.

5. Stellen Sie per SSH eine Verbindung mit dem Clusteredgeknoten oder -hauptknoten her. Verwenden Sie anschließend den SSH-Befehl, um eine Verbindung mit einem Worker- oder Zookeeper-Knoten herzustellen. Die Verbindung wird mit dem weitergeleiteten Schlüssel hergestellt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von SSH-Tunneln mit HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Verwenden von Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [Verwenden von SCP mit HDInsight](./use-scp.md)