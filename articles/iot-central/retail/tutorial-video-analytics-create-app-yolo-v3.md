---
title: 'Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)'
description: In diesem Tutorial wird veranschaulicht, wie Sie in IoT Central eine Anwendung für die Videoanalyse erstellen. Sie erstellen die Anwendung, passen sie an und stellen dafür die Verbindung mit anderen Azure-Diensten her. In diesem Tutorial wird das YOLO v3-System für die Echtzeitobjekterkennung verwendet.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 8066f42a5ab34f5cab0afcdc42cc6ccacfff2855
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876672"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)

Informieren Sie sich als Lösungsentwickler darüber, wie Sie mit der IoT Central-Anwendungsvorlage vom Typ *Videoanalyse: Objekt- und Bewegungserkennung*, Azure IoT Edge-Geräten, Azure Media Services und dem YOLO v3-System für die Echtzeitobjekterkennung und -bewegungserkennung eine Anwendung für die Videoanalyse erstellen. Im Rahmen der Lösung wird anhand eines Einzelhandelsgeschäfts (Filiale) veranschaulicht, wie die häufige geschäftliche Anforderung zur Überwachung von Sicherheitskameras erfüllt wird. Für die Lösung wird die automatische Objekterkennung in einem Videofeed genutzt, um interessante Ereignisse schnell identifizieren und den entsprechenden Ort ermitteln zu können.

> [!TIP]
> Informationen zur Verwendung von OpenVINO&trade; anstelle von YOLO v3 für die Objekt- und Bewegungserkennung finden Sie unter [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Das GitHub-Repository enthält auch den Quellcode für die IoT Edge-Module **LvaEdgeGatewayModule** und **lvaYolov3**. Weitere Informationen zur Verwendung des Quellcodes finden Sie unter [Ändern und Erstellen der Gatewaymodule für die Livevideoanalyse](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Bearbeiten des Bereitstellungsmanifests

Sie stellen ein IoT Edge-Modul bereit, indem Sie ein Bereitstellungsmanifest verwenden. In IoT Central können Sie das Manifest als Gerätevorlage importieren und die Verwaltung der Modulbereitstellung dann IoT Central überlassen.

Bereiten Sie das Bereitstellungsmanifest wie folgt vor:

1. Öffnen Sie die Datei *deployment.amd64.json*, die Sie im Ordner *lva-configuration* gespeichert haben, in einem Text-Editor.

1. Suchen Sie nach den Einstellungen für `LvaEdgeGatewayModule`, und ändern Sie den Bildnamen wie im folgenden Codeausschnitt:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Fügen Sie den Namen Ihres Media Services-Kontos im Abschnitt `LvaEdgeGatewayModule` dem Knoten `env` hinzu. Sie haben sich diesen Kontonamen in der Datei *scratchpad.txt* notiert:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Über die Vorlage werden diese Eigenschaften nicht in IoT Central verfügbar gemacht. Daher müssen Sie die Media Services-Konfigurationswerte dem Bereitstellungsmanifest hinzufügen. Suchen Sie nach dem Modul `lvaEdge`, und ersetzen Sie die Platzhalter durch die Werte, die Sie sich beim Erstellen Ihres Media Services-Kontos in der Datei *scratchpad.txt* notiert haben.

    `azureMediaServicesArmId` ist die **Ressourcen-ID**, die Sie sich beim Erstellen des Media Services-Kontos in der Datei *scratchpad.txt* notiert haben.

    Sie haben sich die Angaben für `aadTenantId`, `aadServicePrincipalAppId` und `aadServicePrincipalSecret` in der Datei *scratchpad.txt* notiert, als Sie den Dienstprinzipal für Ihr Media Services-Konto erstellt haben:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Speichern Sie die Änderungen.

In diesem Tutorial wird Ihre Lösung für die Verwendung des YOLO v3-Moduls für Objekt- und Bewegungserkennung konfiguriert. Der folgende Ausschnitt zeigt die Konfiguration des Moduls:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Ersetzen des Manifests

Wählen Sie auf der Seite **LVA-Edgegateway** die Option **+ Manifest ersetzen** aus.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Manifest ersetzen":::

Navigieren Sie zum Ordner *lva-configuration*, und wählen Sie die Manifestdatei *deployment.amd64.json* aus, die Sie zuvor bearbeitet haben. Wählen Sie die Option **Hochladen**. Wählen Sie nach Abschluss der Validierung die Option **Ersetzen** aus.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
