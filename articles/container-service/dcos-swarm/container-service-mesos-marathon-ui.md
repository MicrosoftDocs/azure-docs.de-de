---
title: (VERALTET) Verwalten eines Azure-DC/OS-Clusters mit der Marathon-Benutzeroberfläche
description: Stellen Sie mit der Marathon-Webbenutzeroberfläche Container für einen Azure Container Service-Clusterdienst bereit.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 16c16c0217a796ffbb57e10430f90cb4a7660ac6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61468225"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(VERALTET) Verwalten eines Azure Container Service-DC/OS-Clusters über die Marathon-Webbenutzeroberfläche

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS stellt eine Umgebung für die Bereitstellung und Skalierung geclusterter Workloads bereit und abstrahiert die zugrunde liegende Hardware. Zusätzlich zu DC/OS ist auch ein Framework vorhanden, mit dem die Planung und Ausführung von Computeworkloads verwaltet wird.

Es sind zwar Frameworks für viele gängige Workloads verfügbar, in diesem Dokument werden jedoch die ersten Schritte beim Bereitstellen von Containern mit Marathon beschrieben. 


## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie diese Beispiele durcharbeiten, benötigen Sie einen DC/OS-Cluster, der im Azure Container Service konfiguriert ist. Sie müssen auch über Remoteverbindungen mit diesem Cluster verfügen. Weitere Informationen zu diesen Elementen finden Sie in den folgenden Artikeln:

* [Bereitstellen eines Azure Container Service-Clusters](container-service-deployment.md)
* [Verbinden mit einem Azure Container Service-Cluster](../container-service-connect.md)

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass das Tunneling zum DC/OS-Cluster über den lokalen Port 80 erfolgt.
>

## <a name="explore-the-dcos-ui"></a>Untersuchen der DC/OS-Benutzeroberfläche
Navigieren Sie [nach der Einrichtung](../container-service-connect.md) eines SSH-Tunnels (Secure Shell) zu „http:\//localhost/“. Dadurch wird die DC/OS-Webbenutzeroberfläche geladen, und es werden Informationen zum Cluster angezeigt, etwa genutzte Ressourcen, aktive Agents und ausgeführte Dienste.

![DC/OS-Benutzeroberfläche](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Erkunden der Marathon-Benutzeroberfläche
Um die Marathon-Benutzeroberfläche anzuzeigen, navigieren Sie zu „http:\//localhost/marathon“. Auf diesem Bildschirm können Sie einen neuen Container oder eine andere Anwendung im Azure Container Service-DC/OS-Cluster starten. Sie können auch Informationen zu ausgeführten Containern und Anwendungen anzeigen.  

![Marathon-Benutzeroberfläche](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Bereitstellen eines Containers im Docker-Format
Klicken Sie zum Bereitstellen eines neuen Containers mit Marathon auf **Anwendung erstellen**, und geben Sie die folgenden Informationen in die Formularregisterkarten ein:

| Feld | Wert |
| --- | --- |
| ID |nginx |
| Arbeitsspeicher | 32 |
| Image |nginx |
| Netzwerk |Überbrückt |
| Hostport |80 |
| Protocol |TCP |

![Neue Anwendungsbenutzeroberfläche – Allgemein](./media/container-service-mesos-marathon-ui/dcos4.png)

![Neue Anwendungsbenutzeroberfläche – Docker-Container](./media/container-service-mesos-marathon-ui/dcos5.png)

![Neue Anwendungsbenutzeroberfläche – Ports und Dienstermittlung](./media/container-service-mesos-marathon-ui/dcos6.png)

Sie müssen den JSON-Modus verwenden, wenn Sie den Containerport statisch einem Port auf dem Agent zuordnen möchten. Wechseln Sie im Assistenten „Neue Anwendung“ hierzu mit dem Umschalter in den **JSON-Modus** . Geben Sie anschließend im Abschnitt `portMappings` der Anwendungsdefinition die folgende Einstellung ein. In diesem Beispiel wird Port 80 des Containers an Port 80 des DC/OS-Agents gebunden. Nachdem Sie diese Änderung vorgenommen haben, können Sie den JSON-Modus für den Assistenten wieder deaktivieren.

```none
"hostPort": 80,
```

![Neue Anwendungsbenutzeroberfläche – Beispiel für Port 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Falls Sie Integritätsprüfungen aktivieren möchten, legen Sie auf der Registerkarte **Integritätsprüfungen** einen Pfad fest.

![Neue Anwendungsbenutzeroberfläche – Integritätsprüfungen](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

Der DC/OS-Cluster wird mit einem Satz von privaten und öffentlichen Agents bereitgestellt. Damit der Cluster auf Anwendungen über das Internet zugreifen kann, müssen Sie die Anwendungen auf einem öffentlichen Agent bereitstellen. Wählen Sie dazu im Assistenten für die neue Anwendung die Registerkarte **Optional**, und geben Sie unter **Accepted Resource Roles** (Akzeptierte Ressourcenrollen) den Text **slave_public** ein.

Klicken Sie dann auf **Anwendung erstellen**.

![Neue Anwendungsbenutzeroberfläche – Einstellung für öffentlichen Agent](./media/container-service-mesos-marathon-ui/dcos14.png)

Auf der Marathon-Hauptseite können Sie den Bereitstellungsstatus für den Container anzeigen. Zu Beginn wird der Status **Wird bereitgestellt...** angezeigt. Nach erfolgreicher Bereitstellung ändert sich der Status in **Wird ausgeführt...** .

![Hauptseite der Marathon-Benutzeroberfläche – Containerbereitstellungsstatus](./media/container-service-mesos-marathon-ui/dcos7.png)

Wenn Sie wieder zur DC/OS-Webbenutzeroberfläche (http:\//localhost/) wechseln, sehen Sie, dass eine Aufgabe – in diesem Fall ein Container im Docker-Format – im DC/OS-Cluster ausgeführt wird.

![DC/OS-Webbenutzeroberfläche – Im Cluster ausgeführte Aufgabe](./media/container-service-mesos-marathon-ui/dcos8.png)

Wenn Sie den Clusterknoten ermitteln möchten, auf dem die Aufgabe ausgeführt wird, klicken Sie auf die Registerkarte **Knoten**.

![DC/OS-Webbenutzeroberfläche – Aufgabenclusterknoten](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Zugreifen auf den Container

In diesem Beispiel wird die Anwendung auf einem öffentlichen Agent-Knoten ausgeführt. Sie greifen aus dem Internet auf die Anwendung zu, indem Sie zum Agent-FQDN des Clusters navigieren: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`. Hierbei gilt Folgendes:

* **DNSPREFIX** ist das DNS-Präfix, das bei der Bereitstellung des Clusters angegeben wurde.
* **REGION** ist die Region, in der sich die Ressourcengruppe befindet.

    ![Nginx aus dem Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Nächste Schritte
* [Verwenden von DC/OS und der Marathon-API](container-service-mesos-marathon-rest.md)

* Deep dive on the Azure Container Service with Mesos (Ausführliche Informationen zu Azure Container Service mit Mesos)

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
