# PowerShellHelloWorld

## 设置执行权限

This is normal behavior. In order to prevent malicious scripts from running on your system, PowerShell enforces an execution policy. There are 4 execution policies you can use:

- Restricted – Scripts won’t run. Period. (Default setting)
- RemoteSigned – Locally-created scripts will run. Scripts that were created on another machine will not run unless they are signed by a trusted publisher.
- AllSigned – Scripts will only run if signed by a trusted publisher (including locally-created scripts).
- Unrestricted – All scripts will run regardless of who created them and whether or not they are signed.

In order to use our newly-created script, we will have to modify our execution policy to allow our script to run. Since we have not digitally signed our new script, our options for setting the execution policy are left to “RemoteSigned” and “Unrestricted.” We are going to change it to RemoteSigned.

In order to change the execution policy, we will need to reopen PowerShell as an Administrator (the command will fail otherwise) and run the following command:

```powershell
Set-ExecutionPolicy RemoteSigned
```

The Set-ExecutionPolicy command will ask to verify that you really want to change the execution policy. Go ahead and select Y for yes, then go ahead and close and reopen your Powershell window.

## 运行脚本

After restarting the Powershell window, go ahead and try running that script again

```powershell
& "C:\Scripts\My First Script.ps1"
```

It should write back, “Hello, World!” to the window: