---
title: Uso del Control de mapa de Azure Location Based Services | Microsoft Docs
description: Aprenda a usar la biblioteca Javascript del lado cliente del Control de mapa de Azure Location Based Services.
services: location-based-services
keywords: No agregue ni edite palabras clave sin consultar a su responsable de SEO Champ.
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 494a8308a5ed4ae37ed9561d051155e7433e6193
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Uso del Control de mapa de Azure Location Based Services
La biblioteca de Javascript del lado cliente del Control de mapa le permite representar mapas y la funcionalidad insertada de Azure Location Based Services en su aplicación web o móvil. 

## <a name="prerequisites"></a>requisitos previos
Una cuenta y una clave de Azure Location Based Services. Para más información sobre cómo crear una cuenta y recuperar una clave, consulte [How to manage your Azure Location Based Services account and keys](how-to-manage-account-keys.md) (Administración de la cuenta y las claves de Azure Location Based Services). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Creación de un nuevo mapa en una página web con la API de Control de mapa
Puede insertar un mapa en una página web mediante la biblioteca de Javascript del lado cliente de Control de mapa.

1. Cree un nuevo archivo y asígnele el nombre MapSearch.html.

2. Agregue las referencias de hoja de estilos y origen de script de Azure Location Based Services al elemento `<head>` del archivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Para representar un nuevo mapa en el explorador, agregue una referencia **#map** en el elemento `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Para inicializar el control de mapa, defina una nueva sección en el cuerpo HTML y cree un script. Use su propia clave de cuenta del script de Azure Location Based Services. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Abra el archivo en el explorador web y vea el mapa representado.