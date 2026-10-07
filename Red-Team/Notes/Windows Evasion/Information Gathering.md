
#### Defender Module for PowerShell

The [Defender Module for PowerShell](https://learn.microsoft.com/en-us/powershell/module/defender/) allows users to do all of what the GUI interface offers and more.


```
Get-Command -Module Defender
```


### Get-MpComputerStatus

Can be used to get `status details` about `antimalware software` (including `Microsoft Defender Antivirus`) on the computer.

```
Get-MpComputerStatus
```


### Get-MpThreat

Can be used to view the history of threats detected on the computer. For example, below we can assume a `Cobalt Strike Beacon` was detected.

```
Get-MpThreat
```


### Get-MpThreatDetection

is a similar command, which allows users to view the threat detection history on a computer. If we wanted to get detection events related to the `Cobalt Strike Beacon` detected, we could specify the `ThreatID` as an additonal parameter.


```
Get-MpThreatDetection -ThreatID 2147894794
```



The last two useful commands we will mention are [Get-MpPreference](https://learn.microsoft.com/en-us/powershell/module/defender/get-mppreference) and [Set-MpPreference](https://learn.microsoft.com/en-us/powershell/module/defender/set-mppreference), which may be used to configure `Defender`. When playing around with `Microsoft Defender Antivirus`, it is often times necessary to `enable/disable real-time protection` to avoid files getting deleted. This can be done quickly with the following command:

```
Set-MpPreference -DisableRealTimeMonitoring $true
```