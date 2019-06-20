---
title: Behandeln von Problemen beim Bereitstellen von OpenShift in Azure | Microsoft-Dokumentation
description: Behandeln von Problemen beim Bereitstellen von OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: af6746e7246b8783e5bdbef34cf1b57427aa7ebb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771276"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Behandeln von Problemen beim Bereitstellen von OpenShift in Azure

Wenn der OpenShift-Cluster nicht erfolgreich bereitgestellt wird, wird im Azure-Portal ein Fehler ausgegeben. Die Ausgabe ist möglicherweise schwer zu lesen, was die Identifizierung der Problemursache erschwert. Suchen Sie in der Ausgabe nach dem Exitcode 3, 4 oder 5. Im Anschluss finden Sie Informationen zu diesen drei Exitcodes:

- Exitcode 3: Benutzername/Kennwort oder Organisations-ID/Aktivierungsschlüssel für Ihr Red Hat-Abonnement ist falsch
- Exitcode 4: Ihre Red Hat-Pool-ID ist falsch oder es sind keine Berechtigungen verfügbar
- Exitcode 5: Docker Thin-Poolvolume konnte nicht bereitgestellt werden

Stellen Sie bei allen anderen Exitcodes eine SSH-Verbindung mit den Hosts her, und sehen Sie sich die Protokolldateien an.

**OpenShift Container Platform**

Stellen Sie eine SSH-Verbindung mit dem Ansible-Playbook-Host her. Verwenden Sie für die Vorlage oder das Marketplace-Angebot den Bastionhost. Von dort aus können Sie eine SSH-Verbindung mit allen anderen Knoten im Cluster (Masterknoten, Infrastrukturknoten, CNS-Knoten, Computeknoten) herstellen. Zum Anzeigen der Protokolldateien müssen Sie über root-Berechtigungen verfügen. „root“ ist standardmäßig für den SSH-Zugriff deaktiviert. Verwenden Sie „root“ daher nicht, um eine SSH-Verbindung mit anderen Knoten herzustellen.

**OKD**

Stellen Sie eine SSH-Verbindung mit dem Ansible-Playbook-Host her. Verwenden Sie für die OKD-Vorlage bis Version 3.9 den master-0-Host. Verwenden Sie für die OKD-Vorlage ab Version 3.10 den Bastionhost. Vom Ansible-Playbook-Host aus können Sie eine SSH-Verbindung mit allen anderen Knoten im Cluster (Masterknoten, Infrastrukturknoten, CNS-Knoten, Computeknoten) herstellen. Zum Anzeigen der Protokolldateien müssen Sie über root-Berechtigungen (sudo su -) verfügen. „root“ ist standardmäßig für den SSH-Zugriff deaktiviert. Verwenden Sie „root“ daher nicht, um eine SSH-Verbindung mit anderen Knoten herzustellen.

## <a name="log-files"></a>Protokolldateien

Die Protokolldateien („stderr“ und „stdout“) für die Hostvorbereitungsskripts befinden sich auf allen Hosts unter `/var/lib/waagent/custom-script/download/0`. Sollte bei der Vorbereitung des Hosts ein Fehler aufgetreten sein, sehen Sie sich diese Protokolldateien an, um den Fehler zu identifizieren.

Wenn die Vorbereitungsskripts erfolgreich ausgeführt wurden, müssen die Protokolldateien im Verzeichnis `/var/lib/waagent/custom-script/download/1` des Ansible-Playbook-Hosts untersucht werden. Ist der Fehler während der eigentlichen Installation von OpenShift aufgetreten, ist der Fehler in der Datei „stdout“ enthalten. Wenden Sie sich mit diesen Informationen an den Support, um weitere Unterstützung zu erhalten.

Beispielausgabe

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Folgende Installationsfehler treten am häufigsten auf:

1. Der private Schlüssel enthält eine Passphrase.
2. Das Schlüsseltresorgeheimnis mit privatem Schlüssel wurde nicht ordnungsgemäß erstellt.
3. Die Dienstprinzipal-Anmeldeinformationen wurden nicht ordnungsgemäß eingegeben.
4. Der Dienstprinzipal verfügt für die Ressourcengruppe nicht über den Zugriffstyp „Mitwirkender“.

### <a name="private-key-has-a-passphrase"></a>Der private Schlüssel enthält eine Passphrase.

Es wird ein Fehler mit dem Hinweis angezeigt, dass die Berechtigung für SSH verweigert wurde. Stellen Sie eine SSH-Verbindung mit dem Ansible-Playbook-Host her, und überprüfen Sie, ob der private Schlüssel eine Passphrase enthält.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Das Schlüsseltresorgeheimnis mit privatem Schlüssel wurde nicht ordnungsgemäß erstellt.

Der private Schlüssel wird in den Ansible-Playbook-Host (~/.ssh/Id_rsa) kopiert. Vergewissern Sie sich, dass diese Datei korrekt ist. Erstellen Sie zum Testen über den Ansible-Playbook-Host eine SSH-Sitzung mit einem der Clusterknoten.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Die Dienstprinzipal-Anmeldeinformationen wurden nicht ordnungsgemäß eingegeben.

In der Eingabe für die Vorlage oder das Marketplace-Angebot wurden falsche Informationen bereitgestellt. Vergewissern Sie sich, dass „appID“ („clientId“) und „password“ („clientSecret“) für den Dienstprinzipal korrekt sind. Führen Sie zur Überprüfung den folgenden Befehl für die Azure-Befehlszeilenschnittstelle aus:

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Der Dienstprinzipal verfügt für die Ressourcengruppe nicht über den Zugriffstyp „Mitwirkender“.

Wenn der Azure-Cloudanbieter aktiviert ist, muss der verwendete Dienstprinzipal über den Zugriffstyp „Mitwirkender“ für die Ressourcengruppe verfügen. Führen Sie zur Überprüfung den folgenden Befehl für die Azure-Befehlszeilenschnittstelle aus:

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Weitere Tools

Bei einigen Fehlern können Sie auch die folgenden Befehle verwenden, um weitere Informationen zu erhalten:

1. systemctl status \<Dienst>
2. journalctl -xe
