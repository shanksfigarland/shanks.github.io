---
title: 'UAC Bypass Techniques'
layout: mypost
categories: [Windows]
---

> IN PROGRESS 
{: .prompt-warning }




## <span style="color: #74C7D2">What is User Account Control (UAC)?</span>

According to [Microsoft](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/):

> User Account Control (UAC) is a Windows security feature designed to protect the operating system from unauthorized changes. When changes to the system require administrator-level permission, UAC notifies the user, giving the opportunity to approve or deny the change. UAC improves the security of Windows devices by limiting the access that malicious code has to execute with administrator privileges. UAC empowers users to make informed decisions about actions that may affect the stability and security of their device.
{: .prompt-info }
> Unless you disable UAC, malicious software is prevented from disabling or interfering with UAC settings. UAC is enabled by default, and you can configure it if you have administrative privileges.
{: .prompt-info }


![uac](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/images/uac-consent-prompt-admin.png)

## <span style="color: #74C7D2">Checking if UAC is enabled</span>

```powershell
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

![uac-disabled](/assets/img/commons/uac-disable.png)


- If 0 then, UAC won't prompt (like disabled)
- If 1 the admin is asked for username and password to execute the binary with high rights (on Secure Desktop)
- If 2 (Always notify me) UAC will always ask for confirmation to the administrator when he tries to execute something with high privileges (on Secure Desktop)
- If 3 like 1 but not necessary on Secure Desktop
- If 4 like 2 but not necessary on Secure Desktop
- If 5(default) it will ask the administrator to confirm to run non Windows binaries with high privileges

My friend [wint3rmuted](https://github.com/wint3rmuted) made a dirty script to check it:

```powershell
# A PowerShell script to query the UAC.
# Retrieves the values of the LocalAccountTokenFilterPolicy and FilterAdministratorToken registry keys, and take user input to save the output to a .txt file
# For Reference: 
# If EnableLUA=0 or doesn't exist, no UAC for anyone
# If EnableLua=1 and LocalAccountTokenFilterPolicy=1 , No UAC for anyone
# If EnableLua=1 and LocalAccountTokenFilterPolicy=0 and FilterAdministratorToken=0, No UAC for RID 500 (Built-in Administrator)
# If EnableLua=1 and LocalAccountTokenFilterPolicy=0 and FilterAdministratorToken=1, UAC for everyone

# Function to print colored text
function Write-ColoredText {
    param (
        [string]$text,
        [string]$color
    )

    Write-Host $text -ForegroundColor $color
}

# Display ASCII banner with colored text
Write-ColoredText @"

 _     _ _______ _______ _______ _______ _______ __   _
 |     | |_____| |       |______ |       |_____| | \  |
 |_____| |     | |_____  ______| |_____  |     | |  \_| 
                                    
                                    by @wint3rmute
 
"@ "Cyan"

# Define the registry path for UAC settings
$uacRegistryPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"

# Get the value of the EnableLUA registry key
$enableLUA = Get-ItemProperty -Path $uacRegistryPath -Name EnableLUA

# Get the value of the ConsentPromptBehaviorAdmin registry key
$consentPromptBehaviorAdmin = Get-ItemProperty -Path $uacRegistryPath -Name ConsentPromptBehaviorAdmin

# Display the UAC settings with colored text
Write-ColoredText "UAC Settings:" "Pink"
Write-ColoredText "EnableLUA: $($enableLUA.EnableLUA)" "White"
Write-ColoredText "ConsentPromptBehaviorAdmin: $($consentPromptBehaviorAdmin.ConsentPromptBehaviorAdmin)" "White"

# Define the registry path for LocalAccountTokenFilterPolicy
$tokenFilterRegistryPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"

# Get the value of the LocalAccountTokenFilterPolicy registry key
$localAccountTokenFilterPolicy = Get-ItemProperty -Path $tokenFilterRegistryPath -Name LocalAccountTokenFilterPolicy

# Get the value of the FilterAdministratorToken registry key
$filterAdministratorToken = Get-ItemProperty -Path $uacRegistryPath -Name FilterAdministratorToken

# Display the values of LocalAccountTokenFilterPolicy and FilterAdministratorToken with colored text
Write-ColoredText "LocalAccountTokenFilterPolicy: $($localAccountTokenFilterPolicy.LocalAccountTokenFilterPolicy)" "White"
Write-ColoredText "FilterAdministratorToken: $($filterAdministratorToken.FilterAdministratorToken)" "White"

# Prompt user for file path to save output
$filePath = Read-Host "Enter the path to save the output (include .txt extension):"

# Create a hashtable with data to be saved
$outputData = @{
    "EnableLUA" = $enableLUA.EnableLUA
    "ConsentPromptBehaviorAdmin" = $consentPromptBehaviorAdmin.ConsentPromptBehaviorAdmin
    "LocalAccountTokenFilterPolicy" = $localAccountTokenFilterPolicy.LocalAccountTokenFilterPolicy
    "FilterAdministratorToken" = $filterAdministratorToken.FilterAdministratorToken
}

# Convert hashtable to string and save to file
$outputData | Out-String | Out-File -FilePath $filePath

Write-ColoredText "Output saved to $filePath" "Green"
```


## <span style="color: #74C7D2">Fodhelper: OSCP</span>

> Fodhelper.exe is one of Windows default executables in charge of managing Windows optional features, including additional languages, applications not installed by default, or other operating-system characteristics. Like most of the programs used for system configuration, fodhelper can auto elevate when using default UAC settings so that administrators won’t be prompted for elevation when performing standard administrative tasks. While we’ve already taken a look at an autoElevate executable, unlike msconfig, fodhelper can be abused without having access to a GUI.
{: .prompt-info }

