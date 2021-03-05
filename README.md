# A Step-by-Step Guide to Connecting the Local VS Studio Code to Google Colab's GPU Runtime

## Requirements
- A Google Account of course :).
- VS Studio Code installed on your operating system.

## Steps
1. First, you need to have a cloudfare binary file. You can download it for your corresponding operating system from [here](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup). Follow all the instructions given [here](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup) to install it.

2. Once cloudflared is installed, you will have a `binary(.exe)` file after extracting the package. Copy the exact path to the binary file. For example, in my case it is in the `C drive`, so I will write the path as `C:\\cloudflared.exe`.
> Note the double backslash `C:\\cloudflared.exe`, one backslash would not do it.

3. Next, open VS Studio Code and install the `Remote-SSH` extension. After installing, press `Ctrl + Shift + P` and search for `Remote Configuration File`. Then, hit enter and paste the following lines in the configuration file:

```
Host *.trycloudflare.com
 HostName %h
 User root
 Port 22
 ProxyCommand <PUT_THE_ABSOLUTE_CLOUDFLARE_PATH_HERE> access ssh --hostname %h
```
> Note that you need to put the absolute path to the binary file which we copied in Step 2.

4. After preparing your configuration file, open Google Colab and launch a new notebook and connect to a GPU runtime if you need one. Also, mount your Google drive if you want to access your drive files. Finally, paste the following snippet of code in a cell and enter a password of your choice:
```
# Install colab_ssh on google colab
!pip install colab_ssh --upgrade

from colab_ssh import launch_ssh_cloudflared, init_git_cloudflared
launch_ssh_cloudflared(password="<PUT_YOUR_PASSWORD_HERE>")

# Optional: if you want to clone a github repository
init_git_cloudflared(githubRepositoryUrl)
```

5. Copy the VS Studio Code Remote SSH hostname. Go back to your VSCode and again hit (Ctrl + Shift + P) and search for Remote:Connect to host.

Paste the VSCode’s hostname and press enter. Select Linux as the platform and hit enter. It will ask for the password you have set and voila you are now connected to Google Colab with GPU access all bundled in your VSCode !!!

You can check if you have GPU access by opening a new terminal and hitting nvidia-smi command.

6. You can open any folder in your Google drive. Just copy the path of the folder and paste it into VSCode.

Things to note:
It may take some time for connecting to the Google Colab via SSH.
You must run python files by using python3 <filename> in the terminal to ensure your files run in Python-3.x. If you use only python <filename> the colab would use Python-2 at the backend.
If you are on Windows:
After you are done with your work in the workspace, always open a powershell window at the cloudflared’s binary file location and execute the following command:
.\cloudflared.exe update
This has to be done because instances of ‘cloudflared’ do not automatically update on Windows which may cause problem for new instances of cloudflared.
3. To avoid the timeout in Google Colab due to inactivity paste the following snippet of code in a cell to keep the server up and running:
while True: pass
4. Keep the Google Colab tab open in the browser.
And that’s it….
You have a full development environment with a GPU access absolutely for free …
