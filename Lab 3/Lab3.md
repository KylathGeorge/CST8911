# CST8911: Lab 3

## Name: Kylath Mamman George

## Student Number: 041198835

### Template

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string"
        },
        "networkInterfaceName": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        },
        "publicIpAddressName": {
            "type": "string"
        },
        "publicIpAddressType": {
            "type": "string"
        },
        "publicIpAddressSku": {
            "type": "string"
        },
        "pipDeleteOption": {
            "type": "string"
        },
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineComputerName": {
            "type": "string"
        },
        "virtualMachineRG": {
            "type": "string"
        },
        "osDiskType": {
            "type": "string"
        },
        "osDiskDeleteOption": {
            "type": "string"
        },
        "virtualMachineSize": {
            "type": "string"
        },
        "nicDeleteOption": {
            "type": "string"
        },
        "hibernationEnabled": {
            "type": "bool"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "secureString"
        },
        "enablePeriodicAssessment": {
            "type": "string"
        },
        "patchMode": {
            "type": "string"
        },
        "rebootSetting": {
            "type": "string"
        },
        "vnetLocation": {
            "type": "string",
            "metadata": {
                "description": "Azure region for the deployment, resource group and resources."
            },
            "defaultValue": "canadacentral"
        },
        "vnetExtendedLocation": {
            "type": "object",
            "defaultValue": {}
        },
        "vnetVirtualNetworkName": {
            "type": "string",
            "defaultValue": "vnet-canadacentral",
            "metadata": {
                "description": "Name of the virtual network resource."
            }
        },
        "vnetTagsByResource": {
            "type": "object",
            "defaultValue": {
                "CST8911VM": "Test"
            }
        },
        "vnetProperties": {
            "type": "object",
            "defaultValue": {
                "subnets": [
                    {
                        "name": "snet-canadacentral-1",
                        "properties": {
                            "addressPrefixes": [
                                "172.16.0.0/24"
                            ]
                        }
                    }
                ],
                "addressSpace": {
                    "addressPrefixes": [
                        "172.16.0.0/16"
                    ]
                }
            }
        },
        "vnetNatGatewaysWithNewPublicIpAddress": {
            "type": "array",
            "defaultValue": []
        },
        "vnetNatGatewaysWithoutNewPublicIpAddress": {
            "type": "array",
            "defaultValue": []
        },
        "vnetNatGatewayPublicIpAddressesNewNames": {
            "type": "array",
            "metadata": {
                "description": "Array of public ip addresses for NAT Gateways."
            },
            "defaultValue": []
        },
        "vnetNetworkSecurityGroupsNew": {
            "type": "array",
            "metadata": {
                "description": "Array of NAT Gateway objects for subnets."
            },
            "defaultValue": []
        },
        "vnetResourceGroupName": {
            "type": "string",
            "defaultValue": "CST8911Lab3"
        },
        "vnetDeploymentName": {
            "type": "string",
            "defaultValue": "network-interface-associated-virtual-network-20251031211226"
        }
    },
    "variables": {
        "subnetRef": "[parameters('subnetId')]",
        "vnetName": "[parameters('vnetVirtualNetworkName')]",
        "standardSku": {
            "name": "Standard"
        },
        "vnetStaticAllocation": {
            "publicIPAllocationMethod": "Static"
        },
        "vnetPremiumTier": {
            "tier": "Premium"
        },
        "vnetPublicIpAddressesTags": "[if(contains(parameters('vnetTagsByResource'), 'Microsoft.Network/publicIpAddresses'), parameters('vnetTagsByResource')['Microsoft.Network/publicIpAddresses'], json('{}'))]",
        "vnetNatGatewayTags": "[if(contains(parameters('vnetTagsByResource'), 'Microsoft.Network/natGateways'), parameters('vnetTagsByResource')['Microsoft.Network/natGateways'], json('{}'))]"
    },
    "resources": [
        {
            "name": "[parameters('networkInterfaceName')]",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2022-11-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', parameters('publicIpAddressName'))]",
                "[parameters('vnetDeploymentName')]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', parameters('publicIpAddressName'))]",
                                "properties": {
                                    "deleteOption": "[parameters('pipDeleteOption')]"
                                }
                            }
                        }
                    }
                ]
            },
            "tags": {
                "CST8911VM": "Test"
            }
        },
        {
            "name": "[parameters('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2023-06-01",
            "location": "[parameters('location')]",
            "properties": {
                "publicIpAllocationMethod": "[parameters('publicIpAddressType')]"
            },
            "sku": {
                "name": "[parameters('publicIpAddressSku')]"
            },
            "tags": {
                "CST8911VM": "Test"
            }
        },
        {
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2024-03-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        },
                        "deleteOption": "[parameters('osDiskDeleteOption')]"
                    },
                    "imageReference": {
                        "publisher": "canonical",
                        "offer": "0001-com-ubuntu-server-jammy",
                        "sku": "22_04-lts-gen2",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]",
                            "properties": {
                                "deleteOption": "[parameters('nicDeleteOption')]"
                            }
                        }
                    ]
                },
                "securityProfile": {},
                "additionalCapabilities": {
                    "hibernationEnabled": false
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "linuxConfiguration": {
                        "patchSettings": {
                            "assessmentMode": "[parameters('enablePeriodicAssessment')]",
                            "patchMode": "[parameters('patchMode')]",
                            "automaticByPlatformSettings": {
                                "rebootSetting": "[parameters('rebootSetting')]"
                            }
                        }
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            },
            "tags": {
                "CST8911VM": "Test"
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2021-04-01",
            "name": "[parameters('vnetDeploymentName')]",
            "resourceGroup": "[parameters('vnetResourceGroupName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Network/virtualNetworks",
                            "apiVersion": "2024-01-01",
                            "name": "[parameters('vnetVirtualNetworkName')]",
                            "location": "[parameters('vnetLocation')]",
                            "extendedLocation": "[if(empty(parameters('vnetExtendedLocation')), json('null'), parameters('vnetExtendedLocation'))]",
                            "tags": "[if(empty(parameters('vnetTagsByResource')), json('{}'), parameters('vnetTagsByResource'))]",
                            "properties": "[parameters('vnetProperties')]"
                        }
                    ]
                }
            },
            "copy": {
                "name": "[parameters('vnetDeploymentName')]",
                "count": 1
            },
            "dependsOn": [
                "natGatewaysWithNewPublicIpAddressCopy",
                "natGatewaysWithoutNewPublicIpAddressCopy",
                "networkSecurityGroupsCopy",
                "natGatewayPublicIpAddressesCopy",
                "natGatewayPublicIpAddressesCopy"
            ]
        },
        {
            "condition": "[greater(length(parameters('vnetNatGatewaysWithoutNewPublicIpAddress')), 0)]",
            "apiVersion": "2019-09-01",
            "type": "Microsoft.Network/natGateways",
            "name": "[parameters('vnetNatGatewaysWithoutNewPublicIpAddress')[copyIndex()].name]",
            "location": "[parameters('vnetLocation')]",
            "tags": "[variables('vnetNatGatewayTags')]",
            "sku": "[variables('standardSku')]",
            "properties": "[parameters('vnetNatGatewaysWithoutNewPublicIpAddress')[copyIndex()].properties]",
            "copy": {
                "name": "natGatewaysWithoutNewPublicIpAddressCopy",
                "count": "[length(parameters('vnetNatGatewaysWithoutNewPublicIpAddress'))]"
            }
        },
        {
            "condition": "[greater(length(parameters('vnetNatGatewaysWithNewPublicIpAddress')), 0)]",
            "apiVersion": "2019-09-01",
            "type": "Microsoft.Network/natGateways",
            "name": "[parameters('vnetNatGatewaysWithNewPublicIpAddress')[copyIndex()].name]",
            "location": "[parameters('vnetLocation')]",
            "tags": "[variables('vnetNatGatewayTags')]",
            "sku": "[variables('standardSku')]",
            "properties": "[parameters('vnetNatGatewaysWithNewPublicIpAddress')[copyIndex()].properties]",
            "dependsOn": [
                "natGatewayPublicIpAddressesCopy"
            ],
            "copy": {
                "name": "natGatewaysWithNewPublicIpAddressCopy",
                "count": "[length(parameters('vnetNatGatewaysWithNewPublicIpAddress'))]"
            }
        },
        {
            "condition": "[greater(length(parameters('vnetNatGatewayPublicIpAddressesNewNames')), 0)]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2023-06-01",
            "name": "[parameters('vnetNatGatewayPublicIpAddressesNewNames')[copyIndex()]]",
            "location": "[parameters('vnetLocation')]",
            "sku": "[variables('standardSku')]",
            "tags": "[variables('vnetPublicIpAddressesTags')]",
            "properties": "[variables('vnetStaticAllocation')]",
            "copy": {
                "name": "natGatewayPublicIpAddressesCopy",
                "count": "[length(parameters('vnetNatGatewayPublicIpAddressesNewNames'))]"
            }
        },
        {
            "condition": "[greater(length(parameters('vnetNetworkSecurityGroupsNew')), 0)]",
            "apiVersion": "2020-05-01",
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[parameters('vnetNetworkSecurityGroupsNew')[copyIndex()].name]",
            "location": "[parameters('vnetLocation')]",
            "tags": "[if(contains(parameters('vnetTagsByResource'), 'Microsoft.Network/networkSecurityGroups'), parameters('vnetTagsByResource')['Microsoft.Network/networkSecurityGroups'], json('{}'))]",
            "properties": {},
            "copy": {
                "name": "networkSecurityGroupsCopy",
                "count": "[length(parameters('vnetNetworkSecurityGroupsNew'))]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[parameters('adminUsername')]"
        }
    }
}
```
