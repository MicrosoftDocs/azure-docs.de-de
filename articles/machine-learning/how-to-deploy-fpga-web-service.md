---
title: Bereitstellen von ML-Modellen in FPGAs
titleSuffix: Azure Machine Learning
description: Informationen zu Field-Programmable Gate Arrays Sie können einen Webdienst auf einem FPGA mit Azure Machine Learning für Rückschlüsse mit extrem geringen Latenzzeiten bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: 9e43291325510f92f2e5fd6c07cdb9257fdede9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033067"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Bereitstellen von ML-Modellen für Field Programmable Gate Arrays (FPGAs) mit Azure Machine Learning 

In diesem Artikel erfahren Sie mehr über FPGAs und die Bereitstellung von ML-Modellen in einem Azure-FPGA mithilfe des [Python-Pakets für hardwarebeschleunigte Modelle](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) von [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>Was sind FPGAs?
FPGAs enthalten ein Array von programmierbaren Logikblöcken sowie eine Hierarchie von neu konfigurierbaren Interconnects. Durch die Interconnects können diese Blöcke nach der Fertigung auf verschiedene Weise konfiguriert werden. Im Vergleich zu anderen Chips bieten FPGAs eine Kombination aus Programmierbarkeit und Leistung. 

FPGAs ermöglichen es, eine geringe Latenzzeit für Echtzeit-Rückschlussanforderungen (oder Modellbewertungsanforderungen) zu erreichen. Asynchrone Anforderungen (Batchverarbeitung) sind nicht erforderlich. Die Batchverarbeitung kann zu Wartezeiten führen, weil mehr Daten verarbeitet werden müssen. Bei Implementierungen von neuronalen Prozessoren ist keine Batchverarbeitung erforderlich. Daher kann die Wartezeit im Vergleich zu CPU- und GPU-Prozessoren um ein Vielfaches geringer ausfallen.

Sie können FPGAs für verschiedene Arten von Machine Learning-Modellen neu konfigurieren. Durch diese Flexibilität ist es einfacher, die Anwendungen auf der Grundlage der optimalen numerischen Genauigkeit und des verwendeten Speichermodells zu beschleunigen. Da FPGAs neu konfigurierbar sind, können Sie mit den Anforderungen der sich schnell ändernden KI-Algorithmen Schritt halten.

![Diagramm zu Azure Machine Learning: FPGA-Vergleich](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Prozessor| Abkürzung |BESCHREIBUNG|
|---|:-------:|------|
|Anwendungsspezifische integrierte Schaltkreise|ASICs|Benutzerdefinierte Schaltkreise, z. B. TPUs (Tensor-Prozessoren) von Google, bieten die höchste Effizienz. Sie können nicht neu konfiguriert werden, wenn sich Ihre Anforderungen ändern.|
|Field-Programmable Gate Arrays|FPGAs|FPGAs, wie sie in Azure verfügbar sind, stellen eine fast so gute Leistung wie ASICs bereit. Sie sind auch flexibel und können im Lauf der Zeit erneut konfiguriert werden, um neue Programmlogik zu implementieren.|
|Grafikprozessoren|GPUs|Eine beliebte Wahl für KI-Berechnungen. GPUs bieten Funktionen zur Parallelverarbeitung und sind beim Grafikrendering schneller als CPUs.|
|Zentralprozessoren|CPUs|CPUs sind Allzweckprozessoren, deren Leistung für die Grafik- und Videoverarbeitung nicht optimal ist.|

## <a name="fpga-support-in-azure"></a>FPGA-Unterstützung in Azure

Microsoft Azure ist die weltweit größte Cloudinvestition in FPGAs. Microsoft verwendet FPGAs für die Auswertung von Deep Neural Networks (DNN), die Rangfolge bei der Bing-Suche und die Beschleunigung von Software Defined Networking (SDN), um die Latenz zu verringern und gleichzeitig CPUs für andere Aufgaben freizugeben.

FPGAs in Azure basieren auf den FPGA-Geräten von Intel, die Data Scientists und Entwickler verwenden, um KI-Echtzeitberechnungen zu beschleunigen. Diese FPGA-fähige Architektur bietet Leistung, Flexibilität und Skalierbarkeit und ist in Azure verfügbar.

Azure-FPGAs sind in Azure Machine Learning integriert. Azure kann vortrainierte DNNs FPGA-übergreifend parallelisieren, um Ihren Dienst aufzuskalieren. Die DNNs können als Deep Featurizer zum Transferlernen vortrainiert oder durch aktualisierte Gewichtungen optimiert werden.

|Szenarien und Konfigurationen in Azure|Unterstützte DNN-Modelle|Regionsunterstützung|
|--------------------------|--------------------|----------------|
|+ Szenarien zur Bildklassifizierung und -erkennung<br/>+ TensorFlow-Bereitstellung (erfordert Tensorflow 1.x)<br/>+ Intel FPGA-Hardware|- ResNet 50<br/>- ResNet 152<br/>- DenseNet-121<br/>- VGG-16<br/>- SSD-VGG|- USA, Osten<br/>- Asien, Südosten:<br/>- Europa, Westen<br/>- USA, Westen 2:|

Um die Latenz und den Durchsatz zu optimieren, sollte sich Ihr Client, der Daten an das FPGA-Modell sendet, in einer der oben genannten Regionen befinden (in derjenigen Region, in der Sie das Modell bereitgestellt haben).

Die **PBS-Familie virtueller Azure-Computer** enthält Intel Arria 10 FPGAs. In der Azure-Kontingentzuteilung wird dafür „Standard PBS Family vCPUs“ angezeigt. Die PB6-VM verfügt über sechs vCPUs und eine FPGA. Die PB6-VM wird von Azure Machine Learning während der Modellimplementierung in einem FPGA automatisch bereitgestellt. Er wird nur mit Azure ML verwendet und kann keine beliebigen Bitströme ausführen. Sie können beispielsweise keine Bitströme auf dem FPGA für Verschlüsselung, Codierung usw. einspielen.

## <a name="deploy-models-on-fpgas"></a>Bereitstellen von Modellen auf FPGAs

Sie können ein Modell als Webdienst auf FPGAs mit [hardwarebeschleunigten Azure Machine Learning-Modellen](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) bereitstellen. Verwenden von FPGAs bietet Rückschlüsse mit extrem geringen Latenzzeiten, sogar mit einer einzigen Batchgröße. 

In diesem Beispiel erstellen Sie ein TensorFlow-Diagramm, um das Eingabebild vorzuverarbeiten, machen daraus mit ResNet50 einen Featurizer für ein FPGA und führen dann die Features über einen mit dem ImageNet-Dataset trainierten Klassifizierer aus. Anschließend wird das Modell in einem AKS-Cluster bereitgestellt.

### <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie noch keines haben, müssen Sie ein Konto mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) erstellen. (Kostenlose Azure-Konten sind nicht für FPGA-Kontingente geeignet.)

- Ein Azure Machine Learning-Arbeitsbereich und das Azure Machine Learning SDK für Python müssen gemäß der Beschreibung in [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md) installiert sein.
 
- Das Paket mit hardwarebeschleunigten Modellen: `pip install --upgrade azureml-accel-models[cpu]`    
    
- Die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- FPGA-Kontingent. Übermitteln Sie eine [Kontingentanforderung](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u), oder führen Sie den CLI-Befehl aus, um das Kontingent zu überprüfen: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Stellen Sie sicher, dass unter __CurrentValue__ mindestens 6 vCPUs zurückgegeben werden.  

### <a name="define-the-tensorflow-model"></a>Definieren des TensorFlow-Modells

Erstellen Sie zunächst mithilfe des [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) eine Dienstdefinition. Eine Dienstdefinition ist eine Datei, die eine Pipeline mit auf TensorFlow basierenden Diagrammen (Eingabe, Featurizer und Klassifizierung) beschreibt. Durch den Bereitstellungsbefehl werden die Definition und die Diagramme in einer ZIP-Datei komprimiert, die dann in Azure Blob Storage hochgeladen wird. Das DNN wurde bereits zur Ausführung im FPGA bereitgestellt.

1. Azure Machine Learning-Arbeitsbereich

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Verarbeiten Sie das Bild vor. Die Eingabe an den Webdienst ist ein JPEG-Bild.  Der erste Schritt besteht im Decodieren und Vorverarbeiten des JPEG-Bilds.  JPEG-Bilder werden als Zeichenfolgen behandelt, und das Ergebnis sind Tensor-Elemente, die als Eingaben für das ResNet 50-Modell verwendet werden.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Laden Sie den Featurizer. Initialisieren Sie das Modell und laden Sie einen TensorFlow-Checkpoint der quantisierten Version von ResNet50 herunter, der als Featurizer verwendet werden soll.  Ersetzen Sie „QuantizedResnet50“ im Codeausschnitt, um andere Deep Neural Networks zu importieren:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Fügen Sie eine Klassifizierung hinzu. Dieser Klassifizierer wurde anhand eines ImageNet-Datasets trainiert.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Speichern Sie das Modell. Nachdem nun der Präprozessor, der ResNet 50-Featurizer und der Klassifizierer geladen wurden, speichern Sie das Diagramm und die zugehörigen Variablen als Modell.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Speichern Sie Eingabe- und Ausgabetensoren, **da Sie diese für Modellkonvertierungs- und Modellrückschlussanforderungen benötigen**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Die folgenden Modelle sind mit ihren Klassifizierungsausgabetensoren für Rückschlüsse aufgelistet, wenn Sie die Standardklassifizierung verwendet haben.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Konvertieren des Modells in das ONNX-Format (Open Neural Network Exchange)

Bevor Sie in FPGAs bereitstellen können, müssen Sie das Modell in das [ONNX](https://onnx.ai/)-Format konvertieren.

1. [Registrieren](concept-model-management-and-deployment.md) Sie das Modell über das SDK anhand der ZIP-Datei in Azure Blob Storage. Das Hinzufügen von Tags und anderen Metadaten zum Modell ermöglicht Ihnen das Nachverfolgen Ihrer trainierten Modelle.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Wenn Sie ein Modell bereits registriert haben und es laden möchten, können Sie es abrufen.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Konvertieren Sie den TensorFlow-Graph in das ONNX-Format.  Sie müssen die Namen der Ein- und Ausgabetensoren angeben, damit sie von Ihrem Client verwendet werden können, wenn Sie den Webdienst nutzen.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Containerisieren und Bereitstellen des Modells

Erstellen Sie dann ein Docker-Image aus dem konvertierten Modell und allen Abhängigkeiten.  Dieses Docker-Image kann dann bereitgestellt und instanziiert werden.  Zu den unterstützten Bereitstellungszielen zählen Azure Kubernetes Service (AKS) in der Cloud oder ein Edge-Gerät (etwa [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md)).  Sie können auch Tags und Beschreibungen für das registrierte Docker-Image hinzufügen.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Listen Sie die Images nach Tag auf, und rufen Sie die ausführlichen Protokolle für Debugzwecke ab.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Bereitstellen in einem Azure Kubernetes Service-Cluster

1. Verwenden Sie AKS, um Ihr Modell als hochgradig skalierbaren Produktionswebdienst bereitzustellen. Sie können mit dem Azure Machine Learning SDK, der CLI oder [Azure Machine Learning Studio](https://ml.azure.com) einen neuen erstellen.

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    Die AKS-Bereitstellung kann etwa 15 Minuten dauern.  Überprüfen Sie, ob die Bereitstellung erfolgreich war.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Stellen Sie den Container im AKS-Cluster bereit.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Bereitstellung auf einem lokalen Edge-Server

Alle [Azure Data Box-Edge-Geräte](../databox-online/azure-stack-edge-overview.md
) enthalten ein FPGA für die Ausführung des Modells.  Es kann nur jeweils ein Modell gleichzeitig auf dem FPGA ausgeführt werden.  Um ein anderes Modell auszuführen, stellen Sie einfach einen neuen Container bereit. Anleitungen und Beispielcode finden Sie in [diesem Azure-Beispiel](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Nutzen des bereitgestellten Modells

Verwenden Sie schließlich den Beispielclient zum Aufrufen des Docker-Images, um Vorhersagen aus dem Modell zu erhalten.  Clientbeispielcode ist verfügbar:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Das Docker-Image unterstützt gRPC und die TensorFlow Serving-API „predict“.

Sie können auch einen Beispielclient für TensorFlow Serving herunterladen.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Da diese Klassifizierung mit dem [ImageNet](http://www.image-net.org/)-Dataset trainiert wurde, ordnen Sie die Klassen den für Menschen lesbaren Bezeichnungen zu.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Vermeiden unnötiger Kosten bereinigen Sie Ihre Ressourcen **in dieser Reihenfolge**: Webdienst, dann Image und dann das Modell.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie, wie Sie Ihr [Webdienstdokument sichern](how-to-secure-web-service.md).

+ Weitere Informationen über den [Preisen und Kosten von Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) und FPGAs

+ [Hyperscalehardware: ML mit Skalierung über Azure und FPGA: Build 2018 (Video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [FPGA-basierte konfigurierbare Cloud von Microsoft (Video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave für KI in Echtzeit](https://www.microsoft.com/research/project/project-brainwave/)

+ [Automatisiertes optisches Untersuchungssystem](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
