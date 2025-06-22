########## DUAL BOOT ##########

cmd(administrator)
bcdedit /export c:\bcdbackup
bcdedit /v
bcdedit /delete {identifier}





########## CHANGE LANGUAGE ##########

Windows 10 Settings > Time & Language > Language tab and then clicking on Add language.

Press Win+R and type cmd, then press Ctrl+Shift+Enter to open Command Prompt with Administrator rights.

Type dism /online /get-packages and press Enter.
We are looking for Microsoft-Windows-Client-LanguagePack-Package as a start for Package Identity. And its State should be Installed Check screenshot
You can see next 5 characters after ~ for understand which language it is. In this example, we are seeing tr-TR. If you are looking for English for example, you should look for en-US.

win+R regedit
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language

Default entry shows that language id of your system. Check this link to look your desired language id from list. Copy the part after 0x part. For example for en-US, get 0409; pt-BR get 0416
Double click to Default (In some cases you might need to change InstallLanguage's id as well) to edit this entry and paste the language id you copied. Save and restart your computer.





########## CHANGE RIGHT-CLICK ##########

win+R regedit

HKEY_CURRENT_USER\SOFTWARE\CLASSES\CLSID

Right-click the CLSID key, select the New menu, and select the Key option.

Name the key {86ca1aa0-34aa-4e8b-a509-50c905bae2a2} and press Enter.

Right-click the newly created key, select the New menu, and select the Key option.

Name the key InprocServer32 and press Enter.

Double-click the newly created key and set its value to blank to enable the classic context menu on Windows 11.





########## HIBERNATE OFF ##########

cmd (administrator)
powercfg -h off