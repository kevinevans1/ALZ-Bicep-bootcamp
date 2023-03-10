## STEP1 Management Groups

```
$dateYMD=$(Get-Date -Format "yyyyMMddTHHmmss")
$NAME="alz-MGDeployment-${dateYMD}"
$LOCATION="canadacentral"
$TEMPLATEFILE="infra-as-code/bicep/modules/managementGroups/managementGroups.bicep"
$PARAMETERS="@infra-as-code/bicep/modules/managementGroups/parameters/managementGroups.parameters.all_mrexample.json"

az deployment tenant create --name $NAME --location $LOCATION --template-file $TEMPLATEFILE --parameters $PARAMETERS
```

## STEP4 Logging & Sentinel

```
$ManagementSubscriptionId="cc99c7cc-f6e0-4034-87c0-dd02d47a1116"
az account set --subscription $ManagementSubscriptionId

$TopLevelMGPrefix="mralzex"

$dateYMD=$(Get-Date -Format "yyyyMMddTHHmmss")
$GROUP="rg-$TopLevelMGPrefix-logging-001"
$NAME="alz-loggingDeployment-${dateYMD}"
$TEMPLATEFILE="infra-as-code/bicep/modules/logging/logging.bicep"
$PARAMETERS="@infra-as-code/bicep/modules/logging/parameters/logging.parameters.all_mrexample.json"
$LOCATION="canadacentral"

# Create Resource Group - optional when using an existing resource group
az group create --name $GROUP --location $LOCATION

# Deploy Module
az deployment group create --name $NAME --resource-group $GROUP --template-file $TEMPLATEFILE --parameters $PARAMETERS
```

## STEP 4.1 MG Diagnostics

```
$TEMPLATEFILE="infra-as-code/bicep/orchestration/mgDiagSettingsAll/mgDiagSettingsAll.bicep"
$PARAMETERS="@infra-as-code/bicep/orchestration/mgDiagSettingsAll/parameters/mgDiagSettingsAll.parameters.all_mrexample.json"
$LOCATION="canadacentral"
$TopLevelMGPrefix="mralzex"

az deployment mg create --template-file $TEMPLATEFILE --parameters $PARAMETERS --location $LOCATION --management-group-id $TopLevelMGPrefix
```

## STEP 5 Network

```
# Set Platform connectivity subscription ID as the the current subscription
$ConnectivitySubscriptionId="8ec5ae04-56b8-4438-b436-ad88abe580dd"

az account set --subscription $ConnectivitySubscriptionId

# Set the top level MG Prefix in accordance to your environment. This example assumes default 'alz'.
$TopLevelMGPrefix="mralzex"

$dateYMD=$(Get-Date -Format "yyyyMMddTHHmmss")
$NAME="alz-HubNetworkingDeploy-${dateYMD}"
$GROUP="rg-$TopLevelMGPrefix-hub-networking-001"
$TEMPLATEFILE="infra-as-code/bicep/modules/hubNetworking/hubNetworking.bicep"
$PARAMETERS="@infra-as-code/bicep/modules/hubNetworking/parameters/hubNetworking.parameters.all_mrexample.json"
$LOCATION="canadacentral"

az group create --location $LOCATION --name $GROUP

az deployment group create --name $NAME --resource-group $GROUP --template-file $TEMPLATEFILE --parameters $PARAMETERS

```
