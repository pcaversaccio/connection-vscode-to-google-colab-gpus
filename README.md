# A Step-by-Step Guide to Connecting the Local VS Studio Code to Google Colab's GPU Runtime

## Requirements
- A Google account of course (yeah I know Google sucks).
- VS Studio Code installed on your operating system.

## Steps
### Step 1
First, you need to have a Cloudfare binary file. You can download it for your corresponding operating system from [here](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation) and follow all the instructions accordingly.

### Step 2
Once `cloudflared` is installed, you will have a `binary(.exe)` file after extracting the package. Copy the exact path to the binary file. For example, if it is the `C:` drive, you will have to write the path as `C:\\cloudflared.exe`.
> Note the double backslash `C:\\cloudflared.exe`, one backslash would not do it!

### Step 3
Next, open VS Studio Code and install the `Remote-SSH` extension. After installing, press `Ctrl + Shift + P` and search for `Remote Configuration File`. Then, hit enter and paste the following lines in the configuration file:
```
Host *.trycloudflare.com
 HostName %h
 User root
 Port 22
 ProxyCommand <PUT_THE_ABSOLUTE_CLOUDFLARE_PATH_HERE> access ssh --hostname %h
```
> Note that you need to put the absolute path to the binary file which we copied in Step 2.

### Step 4
After preparing your configuration file, open [Google Colab](https://colab.research.google.com/) and launch a new notebook and connect to a GPU runtime if you need one. Also, mount your Google drive if you want to access your drive files. 
```
from google.colab import drive
drive.mount('/content/drive')
```
Finally, paste the following code snippet in a cell and enter a password of your choice (generally I prefer: "Gofuckyourself"):
```
# Install colab_ssh on google colab
!pip install colab_ssh --upgrade

from colab_ssh import launch_ssh_cloudflared, init_git_cloudflared
launch_ssh_cloudflared(password="<PUT_YOUR_PASSWORD_HERE>")

# Optional: if you want to clone a github repository
init_git_cloudflared(githubRepositoryUrl)

# To avoid the timeout in Google Colab due to inactivity
while True: pass
```

### Step 5
Copy the appearing ``VSCode Remote SSH`` hostname. Go back to your VS Studio Code and again hit `Ctrl + Shift + P` and search for `Remote:Connect to Host`.

Paste the VS Studio Code's hostname and press enter. Select Linux as the platform and hit enter. It will ask for the password you have set previously and voilà you are now connected to Google Colab with GPU access all bundled in your VS Studio Code!

You can check if you have GPU access by opening a new terminal and hitting the `nvidia-smi` command.

### Step 6
You can open any folder in your Google drive. Just copy the path of the folder and paste it into VS Studio Code. Keep the Google Colab tab open in the browser.

### Note
It may take some time for connecting to the Google Colab via SSH. Also, you must run python files by using `python3 <filename>` in the terminal to ensure your files run in `Python-3.x`. If you use only `python <filename>` the Google Colab would use `Python-2` at the backend.

#### If you are on Windows:
After you are done with your work in the workspace, always open a PowerShell window at the `cloudflared`'s binary file location and execute the following command:
```
.\cloudflared.exe update
```
> This has to be done because instances of `cloudflared` do not automatically update on Windows which may cause problem for new instances of `cloudflared`.

## License
[Do What The F*ck You Want To Public License](https://choosealicense.com/licenses/wtfpl/)