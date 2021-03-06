---
title: "Creación e instalación de archivos de configuración de cliente VPN para conexiones P2S RADIUS: PowerShell (Azure) | Microsoft Docs"
description: "Cree archivos de configuración de cliente VPN en Windows, Mac OS X y Linux para conexiones que usan autenticación RADIUS."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: ce914d2fd0472855ad7a17bf64ae43a76ceb5743
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Creación e instalación de archivos de configuración de cliente VPN para la autenticación P2S RADIUS

Para conectarse de punto a sitio a una red virtual, debe configurar el dispositivo cliente desde el que se va a conectar. Puede crear conexiones VPN de punto a sitio desde dispositivos cliente de Windows, Mac OSX y Linux. Cuando se usa la autenticación RADIUS, existen varias opciones de autenticación: autenticación de nombre de usuario y contraseña, autenticación de certificado, así como otros tipos de autenticación. La configuración de cliente VPN es diferente para cada tipo de autenticación. Para configurar el cliente VPN, use los archivos de configuración de cliente que contienen la configuración necesaria. En este artículo se ayuda a crear e instalar la configuración de cliente VPN para el tipo de autenticación RADIUS que se quiere usar.

El flujo de trabajo de configuración de la autenticación P2S RADIUS es el siguiente:

1. [Configure la puerta de enlace de VPN de Azure para la conectividad P2S](point-to-site-how-to-radius-ps.md).
2. [Configure el servidor RADIUS para la autenticación](point-to-site-how-to-radius-ps.md#radius). 
3. **Obtenga la configuración de cliente VPN correspondiente a la opción de autenticación que prefiera y úsela para configurar el cliente VPN**. (Este artículo)
4. [Complete la configuración de P2S y conéctese](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Si después de generar el perfil de configuración de cliente VPN hay algún cambio en la configuración de VPN de punto a sitio, como el tipo de protocolo de VPN o el tipo de autenticación, debe generar e instalar una nueva configuración de cliente de VPN en sus dispositivos de usuario.
>
>

Para usar las secciones de este artículo, decida primero el tipo de autenticación que quiere usar: nombre de usuario y contraseña, certificado o de otro tipo. En cada sección, hay pasos para Windows, Mac OS X y Linux (pasos limitados disponibles en este momento).

## <a name="adeap"></a>Autenticación con nombre de usuario y contraseña

Hay dos formas de configurar la autenticación de nombre de usuario y contraseña. Puede configurar la autenticación para usar AD o sin usar AD. Asegúrese de que todos los usuarios que se conectan tengan credenciales de nombre de usuario y contraseña que se puedan autenticar mediante RADIUS.

* Al configurar la autenticación de nombre de usuario y contraseña, solo se puede crear una configuración para el protocolo de autenticación de nombre de usuario y contraseña EAP-MSCHAPv2.
* "-AuthenticationMethod" es "EapMSChapv2".

### <a name="usernamefiles"></a> 1. Generación de archivos de configuración de cliente VPN

Genere archivos de configuración de cliente VPN para usar con la autenticación de nombre de usuario y contraseña. Genere los archivos de configuración de cliente VPN con el comando siguiente:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
La ejecución del comando devuelve un vínculo. Copie y pegue el vínculo en un explorador web para descargar el archivo "VpnClientConfiguration.zip". Descomprima el archivo para ver las siguientes carpetas: 
 
* **WindowsAmd64** y **WindowsX86**: estas carpetas contienen los paquetes del instalador de Windows de 64 y 32 bits, respectivamente. 
* **Genérico**: esta carpeta contiene información general que se usa para crear su propia configuración de cliente VPN. Esta carpeta no es necesaria para la configuración de la autenticación del nombre de usuario y la contraseña.
* **Mac**: si configuró IKEv2 cuando creó la puerta de enlace de red virtual, verá una carpeta llamada "Mac" con un archivo **mobileconfig**. Este archivo se usa para configurar clientes Mac.

Si ya ha creado los archivos de configuración del cliente, puede recuperarlos mediante el cmdlet "Get-AzureRmVpnClientConfiguration". Sin embargo, si realiza cambios en la configuración VPN de P2S, como el tipo de autenticación o el tipo de protocolo VPN, la configuración no se actualizará automáticamente. Deberá ejecutar el cmdlet "New-AzureRmVpnClientConfiguration" para crear otra descarga de configuración.

Para recuperar los archivos de configuración de cliente generados anteriormente, use el comando siguiente:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Configuración de clientes VPN

Puede configurar los clientes VPN siguientes:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux con strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Configuración de un cliente VPN en Windows

Puede utilizar el mismo paquete de configuración de cliente VPN en todos los equipos cliente Windows, siempre que la versión coincida con la arquitectura del cliente. Para obtener la lista de sistemas operativos cliente compatibles, consulte la sección sobre las conexiones de punto a sitio en las [preguntas frecuentes](vpn-gateway-vpn-faq.md#P2S).

Use estos pasos para configurar al cliente VPN de Windows nativo para la autenticación mediante certificado:

1. Seleccione los archivos de configuración de cliente VPN que correspondan a la arquitectura del equipo Windows. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86". 
2. Haga doble clic en el paquete para instalarlo. Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**.
3. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta. 

#### <a name="admaccli"></a>Configuración de un cliente VPN en Mac (OSX)

1. Seleccione el archivo **VpnClientSetup mobileconfig** y envíelo a cada uno de los usuarios. Para ello, puede usar el correo electrónico u otro método.

2. Busque el archivo **mobileconfig** en el equipo Mac.

  ![buscar el archivo mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Haga doble clic en el perfil para instalarlo y haga clic en **Continuar**. El nombre del perfil es el mismo que el nombre de la red virtual.

  ![instalar](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Haga clic en **Continuar** para confiar en el emisor del perfil y proseguir con la instalación.

  ![continuar](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Durante la instalación del perfil, tendrá la opción de especificar el nombre de usuario y la contraseña usados para la autenticación de VPN. No es obligatorio especificar esta información. Si lo hace, la información se guarda y se usa automáticamente cuando se inicia una conexión. Haga clic en **Instalar** para continuar.

  ![configuración](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Para instalar el perfil en el equipo, se debe especificar un nombre de usuario y una contraseña para los privilegios necesarios. Haga clic en **OK**.

  ![nombre de usuario y contraseña](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Una vez instalado, el perfil es visible en el cuadro de diálogo **Perfiles**. Este cuadro de diálogo también se puede abrir más adelante desde **Preferencias del sistema**.

  ![preferencias del sistema](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Para acceder a la conexión VPN, abra el cuadro de diálogo **Red** en **Preferencias del sistema**.

  ![red](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. La conexión VPN se muestra como **IkeV2-VPN**. El nombre puede cambiarse mediante la actualización del archivo **mobileconfig**.

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Haga clic en **Configuración de autenticación**. Elija **Nombre de usuario** en la lista desplegable y escriba sus credenciales. Si especificó las credenciales anteriormente, el **nombre de usuario** se elige automáticamente en la lista desplegable y se rellena previamente, junto con la contraseña. Haga clic en **Aceptar** para guardar la configuración. Esta acción le lleva de vuelta al cuadro de diálogo Red.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Haga clic en **Aplicar** para guardar los cambios. Para iniciar la conexión, haga clic en **Conectar**.

#### <a name="adlinuxcli"></a>Configuración de un cliente VPN en Linux con strongSwan

Las siguientes instrucciones se crearon utilizando strongSwan 5.5.1 en Ubuntu 17.0.4. Las pantallas reales pueden variar en función de la versión de Linux y strongSwan.

1. Abra la herramienta **Terminal** para instalar **strongSwan** y su Network Manager (Administrador de red) ejecutando el comando del ejemplo. Si recibe un error relacionado con el paquete "libcharon-extra-plugins", reemplácelo por "strongswan-plugin-eap-mschapv2".

  ```Terminal
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Haga clic en el icono **Network Manager** (Administrador de red) (flecha arriba/flecha abajo) y seleccione **Edit Connections** (Editar conexiones).

  ![editar conexión](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Haga clic en el botón **Add** (Agregar) para crear una conexión.

  ![agregar conexión](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Seleccione **IPsec/IKEv2 (strongswan)** en el menú desplegable y haga clic en **Create** (Crear). Puede cambiar el nombre de la conexión en este paso.

  ![agregar ikev2](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra el archivo **VpnSettings.xml** de la carpeta **Generic** (Genérico) de los archivos de configuración de cliente descargados. Busque la etiqueta denominada **VpnServer** y copie el nombre, que comienza con "azuregateway" y termina con ".cloudapp.net".

  ![configuración de vpn](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Pegue este nombre en el campo **Address** (Dirección) de la nueva conexión VPN en la sección **Gateway** (Puerta de enlace). Luego, haga clic en el icono de carpeta que hay al final del campo **Certificate** (Certificado), vaya a la carpeta Generic (Genérico) y seleccione el archivo **VpnServerRoot** que se encuentra allí.
7. En la sección **Cliente** de la conexión, elija **EAP** como **Autenticación** y escriba el nombre de usuario y contraseña. Es posible que tenga que seleccionar el icono de bloqueo a la derecha para guardar esta información. A continuación, haga clic en **Guardar**.

  ![editar configuración de conexión](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Haga clic en el icono **Network Manager** (Administrador de red) (flecha arriba/flecha abajo) y mantenga el puntero sobre **VPN Connections** (Conexiones VPN). Verá la conexión VPN que ha creado. Para iniciar la conexión, selecciónela para conectar.

  ![conectar radius](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Autenticación de certificados
 
Puede crear archivos de configuración de cliente VPN para la autenticación de certificados RADIUS con el protocolo EAP-TLS. Normalmente, para autenticar a un usuario en una VPN se usa un certificado emitido por Enterprise. Asegúrese de que todos los usuarios que se conectan tengan instalado un certificado en su dispositivo y de que el servidor RADIUS pueda validarlo.
 
* "-AuthenticationMethod" es "EapTls".
* Durante la autenticación de certificado, el cliente valida el servidor RADIUS mediante la validación de su certificado. -RadiusRootCert es el archivo .cer que contiene el certificado raíz que se usa para validar el servidor RADIUS.
* Cada dispositivo de cliente VPN requiere tener instalado un certificado de cliente.
* En ocasiones, un dispositivo Windows tiene varios certificados de cliente. Como consecuencia, durante la autenticación puede aparecer un cuadro de diálogo emergente que muestra todos los certificados. El usuario debe elegir el certificado que quiere usar. Se puede filtrar el certificado correcto mediante la especificación del certificado raíz al que se debe encadenar el certificado de cliente. "-ClientRootCert" es el archivo .cer que contiene el certificado raíz. Es un parámetro opcional. Si el dispositivo desde el que quiere conectarse solo tiene un certificado de cliente, no se debe especificar este parámetro.

### <a name="certfiles"></a>1. Generación de archivos de configuración de cliente VPN

Genere archivos de configuración de cliente VPN para usar con la autenticación de certificado. Genere los archivos de configuración de cliente VPN con el comando siguiente:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

La ejecución del comando devuelve un vínculo. Copie y pegue el vínculo en un explorador web para descargar el archivo "VpnClientConfiguration.zip". Descomprima el archivo para ver las siguientes carpetas:

* **WindowsAmd64** y **WindowsX86**: estas carpetas contienen los paquetes del instalador de Windows de 64 y 32 bits, respectivamente. 
* **GenericDevice**: esta carpeta contiene información general que se usa para crear su propia configuración de cliente VPN.

Si ya ha creado los archivos de configuración del cliente, puede recuperarlos mediante el cmdlet "Get-AzureRmVpnClientConfiguration". Sin embargo, si realiza cambios en la configuración VPN de P2S, como el tipo de autenticación o el tipo de protocolo VPN, la configuración no se actualizará automáticamente. Deberá ejecutar el cmdlet "New-AzureRmVpnClientConfiguration" para crear otra descarga de configuración.

Para recuperar los archivos de configuración de cliente generados anteriormente, use el comando siguiente:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Configuración de clientes VPN

Puede configurar los clientes VPN siguientes:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (compatible, aún no hay ningún artículo con los pasos)

#### <a name="certwincli"></a>Configuración de un cliente VPN en Windows

1. Seleccione un paquete de configuración e instálelo en el dispositivo cliente. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86". Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**. También puede guardar el paquete para instalarlo en otros equipos cliente.
2. Cada equipo cliente debe tener un certificado de cliente para la autenticación. Instale el certificado de cliente. Para obtener información sobre los certificados de cliente, vea [Generación y exportación de certificados para conexiones de punto a sitio con PowerShell en Windows 10](vpn-gateway-certificates-point-to-site.md). Para instalar un certificado que se ha generado, vea [Instalación de un certificado de cliente para conexiones de punto a sitio con autenticación de certificados de Azure](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta.

#### <a name="certmaccli"></a>Configuración de un cliente VPN en Mac (OSX)

Para todos los dispositivos Mac que se conecten a una red virtual de Azure, se debe crear un perfil independiente. El motivo es que estos dispositivos requieren que se especifique el certificado de usuario para la autenticación en el perfil. La carpeta **Genérico** contiene toda la información necesaria para crear un perfil.

  * **VpnSettings.xml** contiene valores de configuración importantes, como el tipo de túnel y la dirección de servidor.
  * **VpnServerRoot.cer** contiene el certificado raíz necesario para validar la puerta de enlace de VPN durante la configuración de la conexión P2S.
  * **RadiusServerRoot.cer** contiene el certificado raíz necesario para validar el servidor RADIUS durante la autenticación.

Use los pasos siguientes para configurar el cliente VPN nativo en Mac para la autenticación mediante certificado:

1. Importe los certificados raíz **VpnServerRoot** y **RadiusServerRoot** en su equipo Mac. Para ello, se puede copiar el archivo en el equipo Mac y hacer doble clic en él.  
Haga clic en **Agregar** para importarlo.

  *Agregar VpnServerRoot*

  ![agregar certificado](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  *Agregar RadiusServerRoot*

  ![agregar certificado](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada equipo cliente debe tener un certificado de cliente para la autenticación. Instale el certificado de cliente en el dispositivo cliente.
3. Abra el cuadro de diálogo **Red** en **Preferencias de red** y haga clic en **"+"** para crear un nuevo perfil de conexión de cliente VPN para establecer una conexión de punto a sitio a la red virtual de Azure.

  El valor de **Interfaz** es "VPN" y el de **Tipo de VPN** es "IKEv2". Especifique un nombre para el perfil en el campo **Nombre del servicio** y, a continuación, haga clic en **Crear** para crear el perfil de conexión de cliente VPN.

  ![red](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. En la carpeta **Genérico**, en el archivo **VpnSettings.xml**, copie el valor de la etiqueta **VpnServer**. Pegue este valor en los campos **Dirección del servidor** e **Id. remoto** del perfil. Deje en blanco el campo **Id. local**.

  ![información del servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Haga clic en **Configuración de autenticación** y seleccione **Certificado**. 

  ![configuración de autenticación](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Haga clic en **Seleccionar...** para elegir el certificado que quiere usar en la autenticación.

  ![certificado](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. En **Choose An Identity** (Elegir una identidad) se muestra una lista de certificados para elegir. Seleccione el certificado adecuado y haga clic en **Continuar**.

  ![identidad](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. En el campo **Id. local**, especifique el nombre del certificado (del paso 6). En este ejemplo, es "ikev2Client.com". A continuación, haga clic en el botón **Aplicar** para guardar los cambios.

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. En el cuadro de diálogo **Red**, haga clic en **Aplicar** para guardar los cambios. A continuación, haga clic en **Conectar** para iniciar la conexión de punto a sitio a la red virtual de Azure.

## <a name="otherauth"></a>Trabajar con otros tipos de autenticación o protocolos

Para usar un tipo de autenticación diferente (por ejemplo, OTP), y no el nombre de usuario y la contraseña, o para usar un protocolo de autenticación diferente (como PEAP-MSCHAPv2, en lugar de EAP-MSCHAPv2), debe crear su propio perfil de configuración de cliente VPN. Para crear el perfil, necesita información como la dirección IP de puerta de enlace de red virtual, el tipo de túnel y las rutas de túnel dividido. Para obtener esta información, siga estos pasos:

1. Use el cmdlet "Get-AzureRmVpnClientConfiguration" para generar la configuración de cliente VPN para EapMSChapv2. Para obtener instrucciones, consulte [esta sección](#ccradius) del artículo.

2. Descomprima el archivo VpnClientConfiguration.zip y busque la carpeta GenenericDevice. Ignore las carpetas que contienen los instaladores de Windows para arquitecturas de 64 y 32 bits.
 
3. La carpeta GenenericDevice contiene un archivo XML llamado VpnSettings. Este archivo contiene toda la información necesaria.

  * VpnServer: FQDN de la instancia de Azure VPN Gateway. Es la dirección a la que se conecta el cliente.
  * VpnType: el tipo de túnel que usa para conectarse.
  * Rutas: rutas que tiene que configurar en el perfil para que solo el tráfico enlazado de red virtual de Azure se envíe a través del túnel P2S.
  * La carpeta GenenericDevice también contiene un archivo .cer denominado "VpnServerRoot". Este archivo contiene el certificado raíz necesario para validar la instancia de Azure VPN Gateway durante la configuración de la conexión de P2S. Instale el certificado en todos los dispositivos que se van a conectar a la red virtual de Azure.

## <a name="next-steps"></a>pasos siguientes

Vuelva al artículo para [completar la configuración de la conexión de punto a sitio](point-to-site-how-to-radius-ps.md).

Para obtener información sobre solución de problemas de P2S, vea [Solución de problemas: conexión de punto a sitio de Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).