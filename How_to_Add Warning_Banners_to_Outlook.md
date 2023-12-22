## How to Add Warning Banners to Outlook mail for impersonation and phishing scams

### External Email Warnings
### If this is your first time running the script from this specific computer
1. open windows powershell as administrator by searching for it in the windows search menu and selecting "Run as administrator"

2. in powershell, enter <span style="color:blue"> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser </span>this allows us to run scripts that we have created locally, but scripts that we download must be digitally signed by a trusted publisher. This only needs to be done once per computer and if you do not do this step you will eventually recieve the error *"Files cannot be loaded because running scripts is disabled on this system. Provide a valid certificate with which to sign the files."*
3. Next, in the powershell after selecting "run as administrator" we need to Install the Exchange Online V3 Module in PowerShell using the following <span style="color:blue">Install-Module -Name PowerShellGet -Force</span> after this is done, close and re-open your powershell and run as administrator
4. next use the following to install the EXO V3 module <span style="color:blue">Install-Module -Name ExchangeOnlineManagement -Force</span>
5. Next enable external tagging in exchange online with "<span style="color:blue">Set-ExternalInOutlook -Enabled $true</span>"
5. Verify external tagging is enabled with "<span style="color:blue">Get-ExternalInOutlook</span>" the output should look like this:
---
RunspaceId : 4b07eecc-34c5-4add-8ee4-80d25aa4aff4

Identity   : 11e55098-68ad-4992-aaf8-c5fdceb3b6da

Enabled    : True   # < External tagging enabled

AllowList  : {}


Note: layout may be different 
---
---
## if this is not your first time running this and you have already completed the above steps on your machine
Note: all commands should be put into the same  powershell using "run as administrator"
1. connect to exchange online with "<span style="color:blue">Connect-ExchangeOnline -userPrincipalName YourEmailHere@YourDomain.com</span>" Note: this email must be a global admin in your domain for the rest to work


4. Add domains to the internal list. This is useful if you are working with another company and no longer want their email to be flagged as an outside domain. To do this enter "<span style="color:blue">Set-ExternalInOutlook -AllowList  @{Add="compusult.com", "compusult.net"}</span>" where emails are replaced eith domains you want to be recognised as internal . To remove domains from this list we use the same command but replace "Add" with "remove". To check the current state of allowed domains we can use "<span style="color:blue">Get-ExternalInOutlook</span>". 
5. after you are finished, use "<span style="color:blue">Disconnect-ExchangeOnline</span>" to disconnect from exchange online

Now you have set domains as internal/external. If you want to set up specific rules for certain words/phrases in the subject or body of the email itself and many more options, go to the microsoft admin center and navigate to mail flow > rules and add or enable/disable various rules 

---
### Adding an impersonation warning

Note: You must have access to a global admin account for your organization for this to work. You must also run all of the rollowing commands through windows powershell which is run as administrator.

1. connect to exchange online by entering "<span style="color:blue">Connect-ExchangeOnline -userPrincipalName YourEmailHere@YourDomain.com</span>" into windows powershell
2. collect all the display names of existing users in your organization using "<span style="color:blue">
$displayNames = (Get-EXOMailbox -ResultSize unlimited  -RecipientTypeDetails usermailbox).displayname</span>". If you want all mailboxes (including shared) then remove "-RecipientTypeDetails usermailbox" from the command above
3. Next we need to create the rule. First define the name with"<span style="color:blue">$transportRuleName = "Impersonation warning"</span>" and then get the existing rule with <span style="color:blue">
$existingTransportRule =  Get-TransportRule | Where-Object {$_.Name -eq $transportRuleName}</span>"
4. Next create the warning banner with html found online. You can just search for "html for email warning banner" and replace the visible text with the message you want.
5. after you are finished, use "<span style="color:blue">Disconnect-ExchangeOnline</span>" to disconnect from exchange online