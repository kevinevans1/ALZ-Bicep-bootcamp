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
$MGPREFIX="mralzex"

az deployment mg create --template-file $TEMPLATEFILE --parameters $PARAMETERS --location $LOCATION --management-group-id $MGPREFIX
```