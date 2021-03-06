---
title: "Creación de una VM a partir de una imagen administrada en Azure | Microsoft Docs"
description: "Cree una máquina virtual con Windows a partir de una imagen administrada generalizada con Azure PowerShell o Azure Portal en el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Creación de una máquina virtual a partir de una imagen administrada

Puede crear varias VM a partir de una imagen de VM administrada con PowerShell o Azure Portal. Una imagen de VM administrada contiene la información necesaria para crear una VM, incluidos los discos del SO y de datos. Los VHD que componen la imagen, incluidos los discos del SO y los discos de datos, se almacenan como discos administrados. 

Debe tener ya [creada una imagen de VM administrada](capture-image-resource.md) para usarla al crear la nueva VM. 

## <a name="use-the-portal"></a>Uso del portal

1. Abra el [Azure Portal](https://portal.azure.com).
2. En el menú izquierdo, seleccione **Todos los recursos**. Puede ordenar los recursos por **Tipo** para localizar fácilmente sus imágenes.
3. Seleccione la imagen que desea usar en la lista. Se abre la página **Información general** de la imagen.
4. Haga clic en **+ Crear VM** en el menú.
5. Escriba la información de la máquina virtual. El nombre de usuario y la contraseña que especifique aquí se usarán para iniciar sesión en la máquina virtual. Cuando haya terminado, haga clic en **Aceptar**. Puede crear la nueva máquina virtual en un grupo de recursos existente, o bien elegir **Crear nuevo** para crear un nuevo grupo de recursos para almacenar la máquina virtual.
6. Seleccione un tamaño para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). 
7. En **Configuración**, realice cambios según sea necesario y haga clic en **Aceptar**. 
8. En la página de resumen, debería ver el nombre de la imagen incluido en **Imagen privada**. Haga clic en **Aceptar** para iniciar la implementación de la máquina virtual.


## <a name="use-powershell"></a>Uso de PowerShell

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que dispone de las versiones más recientes de los módulos AzureRM.Compute y AzureRM.Network PowerShell. Abra un símbolo del sistema de PowerShell como administrador y ejecute el comando siguiente para instalarlo.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).



### <a name="collect-information-about-the-image"></a>Recopilación de información sobre la imagen

Primero se debe recopilar información básica sobre la imagen y crear una variable para la imagen. En este ejemplo se usa una imagen de VM administrada denominada **myImage** que se encuentra en el grupo de recursos **myResourceGroup** en la ubicación **Centro occidental de EE. UU.** 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Crear una red virtual
Cree la red virtual y la subred de la [red virtual](../../virtual-network/virtual-networks-overview.md).

Cree la subred. Este ejemplo crea una subred denominada **mySubnet** con el prefijo de dirección **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Creación de la red virtual. Este ejemplo crea una red virtual denominada **myVnet** con el prefijo de dirección **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Creación de una dirección IP y una interfaz de red

Para permitir la comunicación con la máquina virtual en la red virtual, necesitará una [dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

Cree una dirección IP pública. Este ejemplo crea una dirección IP pública denominada "**myPip**". 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Cree la NIC. Este ejemplo crea una NIC denominada "**myNic**". 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creación del grupo de seguridad de red y las reglas de RDP

Para poder iniciar sesión en la VM mediante RDP, debe tener una regla de seguridad de red (NSG) que permita el acceso RDP en el puerto 3389. 

Este ejemplo crea un NSG denominado"**myNsg**" que contiene una regla llamada "**myRdpRule**" que permita el tráfico RDP en el puerto 3389. Para obtener más información sobre NSG, consulte [Apertura de puertos para una máquina virtual en Azure mediante PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Creación de una variable para la red virtual

Cree una variable para la red virtual completada. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Obtención de las credenciales para la VM

El siguiente cmdlet abrirá una ventana en la que escribirá un nuevo nombre de usuario y una contraseña que se usará como la cuenta de administrador local para tener acceso remoto a la VM. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Definición de las variables para el nombre de VM, el nombre del equipo y el tamaño de la VM

Cree variables para el nombre de VM y el nombre del equipo. Este ejemplo establece el nombre de VM como **myVM** y el nombre del equipo como **myComputer**.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Establezca el tamaño de la máquina virtual. Este ejemplo crea la VM con el tamaño **Standard_DS1_v2**. Consulte la documentación sobre los [tamaños de VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) para más información.

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Agregue el nombre y el tamaño de la VM a la configuración de la VM.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Establecimiento de la imagen de VM como imagen de origen para la nueva VM

Establezca la imagen de origen con el identificador de la imagen de VM administrada.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Establezca la configuración del SO y agregue la NIC.

Escriba el tipo de almacenamiento (PremiumLRS o StandardLRS) y el tamaño del disco del SO. Este ejemplo establece el tipo de cuenta en **PremiumLRS**, el tamaño del disco en **128 GB** y el almacenamiento en caché en disco en **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Creación de la máquina virtual

Cree la nueva VM utilizando la configuración creada y almacenada en la variable **$vm**.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Comprobación de que se creó la máquina virtual
Cuando finalice, debería ver la máquina virtual recién creada en el [Azure Portal](https://portal.azure.com), en **Examinar** > **Máquinas virtuales** o mediante los comandos de PowerShell siguientes:

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes
Para administrar máquinas virtuales con Azure PowerShell, consulte [Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

