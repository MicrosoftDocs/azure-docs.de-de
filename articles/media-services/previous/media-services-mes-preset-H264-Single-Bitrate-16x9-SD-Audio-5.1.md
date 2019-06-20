---
title: H264 Single Bitrate 16x9 SD Audio 5.1 | Microsoft-Dokumentation
description: Das Thema enthält eine Übersicht über die Aufgabenvoreinstellung **H264 Single Bitrate 16x9 SD Audio 5.1**.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d57cd39f-60e8-4248-a21d-c5b9f096b0ce
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 730854f572e1e2ab21140f53d59b7593ad26cb05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463598"
---
# <a name="h264-single-bitrate-16x9-sd-audio-51"></a>H264 Single Bitrate 16x9 SD Audio 5.1
`Media Encoder Standard` definiert eine Reihe von Codierungsvoreinstellungen, die Sie beim Erstellen von Codierungsaufträgen verwenden können. Sie können mithilfe von `preset name` angeben, in welchem Format Ihre Mediendatei codiert werden soll. Oder Sie erstellen eigene JSON- oder XML-basierte Voreinstellungen (mithilfe von UTF-8- oder UTF-16-Codierung). In diesem Fall übergeben Sie die benutzerdefinierte Voreinstellung dann an den Encoder. Eine Liste aller von diesem `Media Encoder Standard`-Encoder unterstützten Voreinstellungsnamen finden Sie unter [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Aufgabenvoreinstellungen für Media Encoder Standard).  
  
 In diesem Thema wird die Voreinstellung `H264 Single Bitrate 16x9 SD Audio 5.1` im XML- und JSON-Format gezeigt.  
  
 Diese Voreinstellung erzeugt eine einzelne MP4-Datei mit einer Bitrate von 2.200 KBit/s und AAC 5.1-Audio. Ausführliche Informationen zu Profil, Bitrate, Samplingrate usw. dieser Voreinstellung finden Sie im unten aufgeführten XML- bzw. JSON-Code. Erläuterungen zur Bedeutung der einzelnen Elemente sowie gültige Werte für jedes Element finden Sie unter [Media Encoder Standard schema](media-services-mes-schema.md) (Media Encoder Standard-Schema).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>2200</Bitrate>  
          <Width>848</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>2200</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 2200,  
          "MaxBitrate": 2200,  
          "BufferWindow": "00:00:05",  
          "Width": 848,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
