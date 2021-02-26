---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956278"
---
Erweitern Sie im Schritt [Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code den Knoten **lva-sample-device** unter **AZURE IOT HUB** (im Abschnitt unten links). Die folgenden bereitgestellten Module sollten angezeigt werden:

* Live Video Analytics-Modul mit dem Namen `lvaEdge`
* Modul `rtspsim`, das einen RTSP-Server als Quelle eines Livevideofeeds simuliert

  ![Module](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> In den obigen Schritten wird vorausgesetzt, dass Sie den mit dem Setupskript erstellten virtuellen Computer verwenden. Gehen Sie wie folgt vor, falls Sie stattdessen Ihr eigenes Edgegerät verwenden: Navigieren Sie zum Gerät, und führen Sie die folgenden Befehle mit **Administratorrechten** aus, um die für diese Schnellstartanleitung verwendete Beispielvideodatei zu pullen und zu speichern:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
