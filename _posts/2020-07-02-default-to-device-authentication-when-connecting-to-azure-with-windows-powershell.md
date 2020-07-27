---
title: "Default to Device Authentication when Connecting to Azure with Windows PowerShell"
categories:
  - Blog
tags:
  - Azure
  - PowerShell
  - Authentication
---

Windows 10 Enterprise edition version 2004 is used for the scenarios demonstrated in this blog
article. If you'd like to follow along, you'll also need to
[install PowerShell version 7](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)
and [the Az PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

As stated in the help for
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount), the
`UseDeviceAuthentication` parameter is the default authentication type for PowerShell version 6 and
higher.

```powershell
help Connect-AzAccount -Parameter UseDeviceAuthentication
```

What this means is that you're provided with a URL and a code. They're used to authenticate with
Azure when signing in interactively from PowerShell.

```powershell
Connect-AzAccount
```

```Output
-UseDeviceAuthentication <System.Management.Automation.SwitchParameter>
    Use device code authentication instead of a browser control. This is the default authentication
    type for PowerShell version 6 and higher.

    Required?                    false
    Position?                    named
    Default value                False
    Accept pipeline input?       False
    Accept wildcard characters?  false
```

By default, Windows PowerShell prompts you to sign in with a GUI:

```powershell
Connect-AzAccount
```

![Microsoft Azure GUI Sign in dialog](/images/az-deviceauth3a.png)

Replicating the default behavior from PowerShell 7 in Windows PowerShell is easy enough using the
`UseDeviceAuthentication` parameter:

```powershell
Connect-AzAccount -UseDeviceAuthentication
```

```Output
PS C:\> Connect-AzAccount -UseDeviceAuthentication
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter
the code FJ92CU6V8 to authenticate.
```

To set this type of behavior as the default in Windows PowerShell, add the `UseDeviceAuthentication`
parameter to your [`$PSDefaultParameterValues` preference
variable](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_parameters_default_values)

```powershell
$PSDefaultParameterValues['Connect-AzAccount:UseDeviceAuthentication']=$true
```

```Output
PS C:\> $PSDefaultParameterValues['Connect-AzAccount:UseDeviceAuthentication']=$true
PS C:\> $PSDefaultParameterValues

Name                           Value
----                           -----
Connect-AzAccount:UseDevice... True

PS C:\>
```

Once added, anytime that you sign in to Azure from Windows PowerShell, it will default to the same
behavior as PowerShell 7:

```powershell
Connect-AzAccount
```

```Output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter
the code FJ92CU6V8 to authenticate.
```

## Troubleshooting

The values stored in `$PSDefaultParameterValues` don't persist between PowerShell sessions. Adding
the items to your
[PowerShell profile](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_profiles)
is the key to making them persist.

Use `$PSDefaultParameterValues` with caution. It could lock you into a particular parameter set as
is the case with the scenario outlined in this blog article.

Don't enclose Boolean values in quotes that you add to `$PSDefaultParameterValues`. Using quotes
causes the values to be added, but not work properly.

```Output
PS C:\> $PSDefaultParameterValues['Connect-AzAccount:UseDeviceAuthentication']="$true"
PS C:\> $PSDefaultParameterValues

Name                           Value
----                           -----
Connect-AzAccount:UseDevice... True
```

## Additional Information

- The end of life for
  [PowerShell version 6](https://docs.microsoft.com/powershell/scripting/powershell-support-lifecycle#powershell-releases-end-of-life)
  is September 4, 2020. Update to PowerShell version 7.
- [Migrate from the AzureRM to the Az PowerShell module](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az).
  AzureRM will continue to receive bug fixes until at least December 2020.

µ
