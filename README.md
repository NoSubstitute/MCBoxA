# MCBoxA
MCBoxA - Move Chromeboxes Automatically

MCBoxA was developed to support automatic relocation of Chromeboxes. MCBoxA has an overview of every Chromebox' serial number, most recent user, where it is located (Device_OU) and where the most recent user is located (MRU_OU). It uses GAMADV-XTD3 to push fresh data to the two sheets Devices & Users, and then uses a number of formulas to calculate their current relationship, and decide what to do with the devices.						

I definitely recommend you read the contents of the scripts before running any functions. If for nothing else at least so you know it looks like it'll only do what I say.

_**Any existing scripts can't do anything at all until you authorise them.**_

### Link to copyable MCBoxA.

[MCBoxA template](https://docs.google.com/spreadsheets/d/1qT1E6aUzULE9KyI-_hLuPKOxyC3DgBN-Rxwbpc6GSQo/copy)

Click the link with your Chromebook admin (often superadmin) account.

All headers have small comments on what you can do and shouldn't.

### GAMADV-XTD3 commands to upload the Device & User data to the Sheet.

Upload device data to the sheet/tab called _Chromeboxes_ - you need to replace the FileID with your copy of the Sheet

```gam config csv_output_row_filter "'^model$:regex:Chromebox','^status$:regex:active'" redirect csv - multiprocess todrive tdfileid ReplaceWithYourFileID tdtitle "MCBoxA" tdsheet Chromeboxes tdupdatesheet true print cros fields serialNumber,osVersion,lastSync,lastEnrollmentTime,model,ou,recentusers,status listlimit 1```

Upload user data to the sheet/tab _Users_ - again you need to replace the FileID with your copy of the Sheet.

```gam config csv_output_row_filter "'^orgUnitPath$:regex:Students|Staff'" redirect csv - multiprocess todrive tdfileid ReplaceWithYourFileID tdtitle "MCBoxA" tdsheet Users tdupdatesheet true print users fields ou,lastlogintime,suspended```

Do note that I am here only pulling users from the OUs that contain the words _Students_ and _Staff_.

### GAMADV-XTD3 command to Move Chromeboxes
You need to replace the FileID with your copy of the Sheet, and reference a user with access to read the Sheet.

```gam config csv_input_row_filter "'^STATUS$:regex:^ROOT$|^MISMATCH A$|^MATCH SCHOOL$|^SAME$'" csv_input_row_drop_filter "'^recentUsers.email$:regex:^ExcludeThis.User@YouDomain.com$|^AlsoExcludeThis.User@YouDomain.com$','^orgUnitPath$:regex:ADMINS|SIGN|DISABLED|DEPROVISIONED','^MRU_OU$:regex:^$'" redirect stdout ./logfile.log multiprocess redirect stderr stdout csv gsheet UserWith.AccessToSheet@Yourdomain.com ReplaceWithYourFileID TEST gam cros_sn "~serialNumber" update ou "~USER2CBOX_OU" assetid "MCBoxA $(date +'%Y-%m-%d %H:%M')"```

Do note that I am excluding some users (IT staff who often touch student devices, and therefore MCBoxA shouldn't move those devices), and devices in some OU paths shouldn't be moved (where we keep Disabled & Deprovisioned devices). Also note that I deliberately refer to the TEST tab, so you don't accidentally run on your entire device population. Change that to MCBoxA when you're good and ready.

## PRIVACY POLICY

**You are in full control of all the data. The script is only accessing data you yourself have access to. No data is sent anywhere, except between you and Google. No matter if you create a copy of my Sheet or my scripts, I will never gain access to any of your data. All data reading and writing is done by you on your own account.**


[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.me/NoSubstitute)
