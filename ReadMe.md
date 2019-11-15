[Full length tutorial here](https://www.darkoperator.com/blog/2013/3/5/powershell-basics-execution-policy-part-1.html). This is just a simplified version for myself.

## Making a Certificate:

1. Locate makecert.exe (command line).
   
    ``` powershell
    where /R "C:\Program Files (x86)\Windows Kits" makecert.*
    ```

2. Create Local Certificate Root, where -makecert location- is the full path of any of the makecert.exe locations from Step 1 (also command line).
    ``` powershell
    "-makecert location-" -n "CN=PowerShell Local Certificate Root" -a sha1 -eku 1.3.6.1.5.5.7.3.3 -r -sv root.pvk root.cer -ss Root -sr localMachine
    ```
    Choose a password to use.

3. Windows Key + R -> Run 'mmc' to open the Microsoft Management Console.

4. File -> Add/Remove Snap-in.

5. Certificates -> Add.

6. Select 'My user account' -> Finish.

7. Click 'Okay'.

8. Create Personal Certificate, where -custom name- is any name you want to show as the owner of this certificate / any scripts signed with it and -makecert location- is the full path of any of the makecert.exe locations from Step 1 (command line).
    ``` powershell
    "-makecert location-" -pe -n "CN=-custom name-" -ss MY -a sha1 -eku 1.3.6.1.5.5.7.3.3 -iv root.pvk -ic root.cer
    ```
    Enter password chosen in Step 2.

## Signing a Powershell Script with the Certificate:

1. In Powershell, run 2 commands, where -path to script- is the path to the script that needs the certificate: 
   
    ``` powershell
    $acert = (dir Cert:\CurrentUser\My -CodeSigningCert)[0]
    Set-AuthenticodeSignature -path to script- -Certificate $acert 
    ```

2. This will append a signature block to the end of the given script. If outside changes to the script are made, the 'Set-AuthenticodeSignature' command will have to be run again.

## Exporting the Certificate:

1. In Microsoft Management Console from 'Making a Certificate', expand the 'Personal' folder and enter 'Certificates'.

2. Right click the certificate to export and select "All Tasks" -> "Export".

3. In the Exporting Wizard: 
   
   1. Next
   
   2. Choose 'Yes, export the private key' -> Next
   
   3. Next (use the default options)
   
   4. Choose 'Password' -> Enter a password for users to use when importing the certificate -> Next
   
   5. Save the certificate -> Next
   
   6. Finish

4. Certificate can now be sent to anyone and imported on their machine just by double clicking.
