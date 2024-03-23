# Office 365 Remove mailbox folder permissions recursively and in Bulk

The script enumerates the default and user-created folders for one or more mailboxes and removes permissions for the specified users. The following parameters are supported:
* __Mailbox__: used to designate the mailbox on which permissions will be adjusted. Any valid Exchange mailbox identifier can be specified. Multiple delegates can be specified in a comma-separated list or array. You can also use the Identity parameter as alias. 
* __User__: used to designate the user for which permissions will be removed. Any valid Exchange security principal can be specified, including Security groups. Multiple delegates can be specified in a comma-separated list or array. You can also use the Delegate parameter as alias. 
* __WhatIf__: used to run the script in a “simulation” mode, without making any actual changes. Works the same way as with other Exchange cmdlets.
* __Verbose__: used to force the script to provide additional details on the cmdlet progress. Useful when troubleshooting issues.
* __Quiet__: used to suppress output to the console. By default, each added/changed permission entry will be displayed in the console, apart from saving it to the CSV file.

As the full list of mailboxes and their folders needs to be cycled in order to set the permissions, the script will use Invoke-Command in order to get a minimum set of attributes returned. In case you run into throttling or connectivity errors, consider adjusting the artificial delay added on line 131.
  
To further reduce the time to execute the script, consider limiting the list of folders to only those you are interested in. This can be achieved by editing the $includedfolders and $excludedfolders arrays:
```PowerShell
$includedfolders = @("Root","Inbox","Calendar", "Contacts", "DeletedItems", "Drafts", "JunkEmail", "Journal", "Notes", "Outbox", "SentItems", "Tasks", "CommunicatorHistory", "Clutter", "Archive")   
   
$excludedfolders = @("News Feed","Quick Step Settings","Social Activity Notifications","Suggested Contacts", "SearchDiscoveryHoldsUnindexedItemFolder", "SearchDiscoveryHoldsFolder")
```
The script does not handle connectivity to Exchange Online, due to the variety of methods now available. If any existing session is detected, it will be used to run the script, otherwise an error will be thrown. Exchange on-premises is also supported, as is running the script in the EMS, but those scenarios are not extensively tested. The script requires PowerShell v3 at minimum.
 
Here are some example uses of the script. To remove permissions on all folders in a specific mailbox for delegate UserX, use:
```PowerShell
.\Remove_Folder_Permissions_recursive_BULK.ps1 -Mailbox sharednew -User UserX
```
To remove permissions on all folders in multiple mailboxes, use:
```PowerShell
.\Remove_Folder_Permissions_recursive_BULK.ps1 -Mailbox shared1,shared2 -User john@contoso.com
```
You can also directly use the output of Get-Mailbox or Get-User to provide values. When Bulk adding permissions, it's strongly advised to use the -WhatIf switch in order to preview the changes first, as well as the -Verbose switch:
```PowerShell
.\Remove_Folder_Permissions_recursive_BULK.ps1 -Mailbox (Get-Mailbox -RecipientTypeDetails RoomMailbox) -User (Get-User -Filter {Department -eq "Legal"}) -WhatIf -Verbose
```
By default the script outputs the results to a CSV file and also stores them in the $varFolderPermissionsRemoved variable. To suppress the Console output, use the -Quiet switch.

Additional information about the script can be found in the built-in help or in [this article](https://www.michev.info/Blog/Post/2063/managing-mailbox-permissions-on-the-folder-level-in-bulk).

Questions and feedback are welcome.
