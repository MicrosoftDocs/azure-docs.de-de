---
title: Erstellen von Linux-Images ohne Bereitstellungs-Agent
description: Erfahren Sie, wie Sie in Azure generalisierte Linux-Images ohne Bereitstellungs-Agent erstellen.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 9f0309f4e8273c2ef19ea86636de8e3aa6b6c4bc
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435099"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Erstellen generalisierter Images ohne Bereitstellungs-Agent

Microsoft Azure bietet Bereitstellungs-Agents für Linux-VMs in Form von [walinuxagent](https://github.com/Azure/WALinuxAgent) oder [cloud-init](https://github.com/canonical/cloud-init) (empfohlen). Es kann jedoch Situationen geben, in denen Sie keine dieser Anwendungen für Ihren Bereitstellungs-Agent verwenden möchten, wie z. B:

- Die Linux-Distribution/-Version unterstützt cloud-init bzw. den Linux-Agent nicht.
- Es müssen bestimmte VM-Eigenschaften festgelegt werden, z. B. Hostname.

> [!NOTE] 
>
> Wenn Sie keine Eigenschaften festlegen müssen oder keine Form von Bereitstellung benötigen, sollten Sie die Erstellung eines speziellen Images in Betracht ziehen.

In diesem Artikel wird gezeigt, wie Sie Ihr VM-Image so einrichten können, dass es die Anforderungen der Azure-Plattform erfüllt, und den Hostnamen festlegen, ohne einen Bereitstellungs-Agent zu installieren.

## <a name="networking-and-reporting-ready"></a>Netzwerkeinrichtung und Melden des Status „Bereit“

Damit Ihre Linux-VM mit Azure-Komponenten kommunizieren kann, benötigen Sie einen DHCP-Client, um eine Host-IP-Adresse aus dem virtuellen Netzwerk abzurufen, sowie eine DNS-Auflösungs- und -Routenverwaltung. Die meisten Distributionen bieten diese Hilfsprogramme standardmäßig. Zu den Tools, die von Linux-Distributionsanbietern mit Azure getestet wurden, gehören u. a. `dhclient`, `network-manager` und `systemd-networkd`.

> [!NOTE]
>
> Derzeit wird das Erstellen generalisierter Images ohne Bereitstellungs-Agent nur von DHCP-fähigen VMs unterstützt.

Nachdem das Netzwerk eingerichtet und konfiguriert wurde, müssen Sie den Status „Bereit“ melden. Dadurch wird Azure mitgeteilt, dass die VM erfolgreich bereitgestellt wurde.

> [!IMPORTANT]
>
> Wenn Azure nicht der Status „Bereit“ gemeldet wird, erfolgt ein Neustart des virtuellen Computers.

## <a name="demosample"></a>Demo/Beispiel

Diese Demo zeigt, wie Sie aus einem vorhandene Marketplace-Image (in diesem Fall einer Debian Buster-VM) den Linux-Agent (walinuxagent) entfernen können. Gleichzeitig wird der Prozess eingerichtet, um Azure so einfach wie möglich zu melden, dass die VM „bereit“ ist.

### <a name="create-the-resource-group-and-base-vm"></a>Erstellen Sie die Ressourcengruppe und Basis-VM:

```bash
$ az group create --location eastus --name demo1
```

Erstellen Sie die Basis-VM:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Entfernen des Bereitstellung-Agents aus dem Image

Nach Bereitstellung der VM können Sie sich über SSH mit ihr verbinden und den Linux-Agent entfernen:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Hinzufügen des erforderlichen Codes zur VM

Innerhalb der VM müssen wir auch, da wir den Azure-Linux-Agent entfernt haben, einen Mechanismus bereitstellen, mit dem wir „Bereit“ melden können. 

#### <a name="python-script"></a>Python-Skript

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Allgemeine Schritte (ohne Python)

Wenn Python nicht in Ihrer VM installiert oder verfügbar ist, können Sie die obige Skriptlogik anhand der folgenden Schritte programmgesteuert reproduzieren:

1. Rufen Sie `ContainerId` und `InstanceId` durch Analysieren der Antwort von WireServer ab: `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate`.

2. Erstellen Sie die folgenden XML-Daten, indem Sie die analysierten Werte für `ContainerId` und `InstanceId` aus dem obigen Schritt einfügen:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Senden Sie diese Daten an WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatisieren der Ausführung des Codes beim ersten Start

In dieser Demo wird systemd verwendet, das häufigste Initialisierungssystem moderner Linux-Distributionen. Die einfachste und nativste Möglichkeit sicherzustellen, dass dieser Mechanismus zum Melden von „Bereit“ zum richtigen Zeitpunkt ausgeführt wird, ist also die Erstellung einer Diensteinheit für systemd. Sie können `/etc/systemd/system` die folgende Einheitsdatei hinzufügen (in diesem Beispiel wird die Einheitsdatei `azure-provisioning.service` genannt):

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Dieser Dienst systemd führt zur grundlegenden Bereitstellung drei Aufgaben aus:

1. Meldet Azure den Status „Bereit“ (um auf die erfolgreiche Einrichtung hinzuweisen)
1. Benennt die VM basierend auf dem vom Benutzer angegebenen VM-Namen um, indem diese Daten aus [Azure Instance Metadata Service (IMDS)](./instance-metadata-service.md) abgerufen werden **Hinweis:** IMDS stellt auch andere [Instanzmetadaten](./instance-metadata-service.md#access-azure-instance-metadata-service) bereit, z. B. öffentliche SSH-Schlüssel, daher können Sie mehr als den Hostnamen festlegen.
1. Deaktiviert sich selbst, sodass dieser Dienst nur beim ersten Start und nicht bei nachfolgenden Neustarts ausgeführt wird

Wenn sich die Einheit im Dateisystem befindet, führen Sie folgenden Befehl aus, um sie zu aktivieren:

```
$ sudo systemctl enable azure-provisioning.service
```

Nun kann die VM generalisiert und ein Image von ihr erstellt werden. 

#### <a name="completing-the-preparation-of-the-image"></a>Abschließen der Vorbereitung des Images

Führen Sie auf Ihrem Entwicklungscomputer die folgenden Schritte aus, um das Erstellen des Images anhand der Basis-VM vorzubereiten:

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

Erstellen Sie das Image anhand dieser VM:

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Jetzt sind wir bereit, eine neue VM (oder mehrere VMs) anhand des Images zu erstellen:

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Es ist wichtig, `--enable-agent` auf `false` festzulegen, da walinuxagent auf dieser VM, die anhand des Images erstellt werden soll, nicht vorhanden ist.

Diese VM sollte erfolgreich bereitgestellt werden. Nach dem Anmelden bei der neu bereitgestellten VM sollten die Ausgabe des Diensts systemd den Status „Bereit“ melden:

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Support

Wenn Sie Ihren eigenen Bereitstellungscode/-Agent implementieren, sind Sie für den Support dieses Codes verantwortlich. Der Microsoft-Support untersucht lediglich Probleme im Zusammenhang mit der Nichtverfügbarkeit der Bereitstellungsschnittstellen. Wir arbeiten ständig an Verbesserungen und Änderungen in diesem Bereich. Daher müssen Sie auf Änderungen bei cloud-init und an der Bereitstellungs-API des Azure-Linux-Agents achten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Bereitstellung von Linux](provisioning.md).