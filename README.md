# rhcl-i
redhat dev installation

Prerequisites for running CDK
The prerequisites for running CDK are:

A Red Hat Developer Program username and password are required to download CDK and to enable the Red Hat Enterprise Linux VM included with the CDK to download container images from Red Hat.  Please make sure to accept the terms and conditions.
A virtualization platform (hypervisor): Hyper-V or VirtualBox.
25 GB of free disk space.
Hypervisor
CDK includes a prebuilt Red Hat Enterprise Linux virtual machine with OpenShift and other container tools pre-installed. To run the VM a hypervisor is required. Please make sure that the virtualization hardware extensions (VT-x or AMD-V) are enabled in your system’s BIOS. 

Hyper-V
Hyper-V is the default virtualization platform for CDK on Windows, but must be enabled as a Windows optional feature. Hyper-V isn’t available on some versions of Windows. VirtualBox is supported as an alternative on all versions of Windows.

In Hyper-V, you must create an external virtual network switch and attach it to your physical NIC (wired or wireless, which ever is connected) to enable internet access for downloading container images.  You must set the environment variable HYPERV_VIRTUAL_SWITCH with the name of the virtual switch. 

C:\> setx HYPERV_VIRTUAL_SWITCH “External (Wireless)”
 

The above setx command will permanently store the environment variable, but you must restart your shell for this setting to take effect. See Windows Shells and Environment Variables below for more information.

For additional information on Hypervisor configuration see Set up hypervisor on Windows in the CDK Getting Started Guide. 
 

VirtualBox
CDK supports VirtualBox 5.1.14 and later on all platforms. No additional downloads are required to use VirtualBox.

Since VirtualBox is not the default hypervisor, you will either need to pass the command line --vm-driver virtualbox to the minishift start command or make the setting persistent with the following command:

C:\> minishift config set vm-driver virtualbox
 

Verify the configuration setting with:

C:\> minishift config get vm-driver
 

Minishift persistent configuration is stored in the .minishift directory which is located in your home directory. If you delete %USERPROFILE%/.minishift you will need to run the commands to configure any persistent settings again. See Persistent Configuration in the CDK Getting Started Guide for more information.

Notes for Windows users
Windows shells and environment variables
You can run CDK using cmd.exe or PowerShell. Working with CDK and minishift requires setting a number of environment variables. You may encounter CDK information that shows the syntax for the bash shell, as that syntax can be used with Linux or macOS. The syntax for setting environment variables is different for cmd.exe and PowerShell. There are also some differences in the quoting that is required for arguments that include spaces or special characters. If you run into problems, check that you’ve correctly set any required environment variables, verify that any variables contain the expected values with set (for cmd.exe) or Get-ChildItem ENV: for PowerShell.

When setting environment variables it is important to understand the scope of the change. Using set VARIABLE=value in cmd.exe or $env:VARIABLE = value in PowerShell only affects the running shell and any processes started from that shell. When a new shell is started or the system is rebooted, the environment variable will no longer be set.

Environment variable settings can be made persistent by storing them in the user’s profile as part of the registry. This can be done with the setx command or by using Windows Control Panel. However, these methods will not affect any running processes. You must start a new command shell for those changes to become part of your runtime environment.
 

Adding a directory to your PATH
CDK includes two command line tools, minishift.exe and oc.exe that need to be located in a directory that is part of your command search path in order to run those commands from the shell. The PATH environment variable contains the list of directories to search for commands.

You can temporarily add a directory to the command search path by setting the PATH environment variable in the current shell. The change will only affect the running shell and any processes started from that shell. If you start a new shell, you will need to change the PATH environment variable again.

To permanently change your PATH, it is recommended to use the Windows Control Panel instead of the setx command described above. Environment variable settings are found under Control Panel -> System -> Advanced System Settings -> Environment Variables. You can also get there directly by searching for Environment Variables in the Start Menu and selecting Edit Environment Variables for Your Account. These changes will not affect any currently running shells. Close any open shells and start a new shell.

Only the directory containing minishift.exe should be permanently added to the PATH. The directory containing oc.exe can change and should be added dynamically to the running shell with minishift oc-env.

Note: Only make changes to User Environment Variables. There is a PATH environment variable under both User Environment Variables and System Environment Variables. The User’s PATH will be used for shells running under your regular user account.
User's home directory
CDK will store state and configuration in the .minishift sub-directory of the user’s home directory. On Windows, the path will be %USERPROFILE%/.minishift, which is typically in C:\user\username\.minishift. You may encounter CDK information that uses the Bash shorthand ~/ to refer to the user’s home directory, for example ~/.minishift. The Windows equivalent for ~/ is %USERPROFILE%/. Note: Using PowerShell the environment variable syntax is $env:USERPROFILE.

 

2. Setup CDK/Minishift   5 minutes
The CDK download is a single executable which includes a number of bundled components. You will use the minishift command to start, stop, and configure the Red Hat Enterprise Linux 7 VM and the Red Hat OpenShift Container Platform single-node cluster running inside of the VM. To run minishift commands, you will need to move the minishift download to a directory that is in your command search path.

The oc command line tool which is used for interacting with OpenShift clusters will be downloaded when you run minishift setup-cdk. The oc command must match the version of OpenShift running inside of the VM.  The minishift oc-env command provides the statements to add the correct version of oc to your PATH. The output of minishift oc-env shows the commands to use in your shell to dynamically update the environment after running minishift oc-env.

The setup steps are:

Copy cdk-minishift-* into a directory that is in your path
Run minishift setup-cdk. to set up your CDK environment in %USERPROFILE%/.minishift
Add your Red Hat Developer Program credentials to your environment
Setup
Adding minishift to your path
The cdk-minishift-* file you downloaded in the first step is an executable. To run minishift commands, copy it to a directory that is in already in your PATH, or create a directory and add it to the PATH environment variable. The directory must be on your system (C:) drive. When copying, rename the downloaded file to minishift.exe.

For cmd.exe

C:\> mkdir %USERPROFILE%/bin
C:\> copy %USERPROFILE/Downloads/cdk-3.4.0-2-minishift* %USERPROFILE%/bin/minishift.ex
 

For PowerSHell

PS C:\> mkdir $env:USERPROFILE/bin
PS C:\> copy $env:USERPROFILE/Downloads/cdk-3.4.0-2-minishift* $env:USERPROFILE/bin/minishift.exe
 

if the directory isn’t already in your PATH, use Windows Control Panel to update your PATH environment variable. Note: You will need to start a new shell window for the change to take effect. See Windows Shells and Environment Variables above for more information.

Run setup-cdk
Run minishift setup-cdk to setup the components needed to run CDK on your system. By default, minishift setup-cdk places CDK content in %USERPROFILE%/.minishift.

C:\> minishift setup-cdk
Setting up CDK 3 on host using '/home/user/.minishift' as Minishift's home directory
Copying minishift-rhel7.iso to '/home/user/.minishift/cache/iso/minishift-rhel7.iso'
Copying oc to '/home/user/.minishift/cache/oc/v3.9.31/linux/oc'
Creating configuration file '/home/user/.minishift/config/config.json'
Creating marker file '/home/user/.minishift/cdk'
Default add-ons anyuid, admin-user, xpaas, registry-route, che, eap-cd installed
Default add-ons anyuid, admin-user, xpaas enabled
CDK 3 setup complete.
 

Note: If you would like to use a different directory see Environment Variables in the CDK Getting Started Guide.

If you delete %USERPROFILE%/.minishift, you will need to run this step again.

Add Red Hat Developer Program username and password to your environment
The VM will need to register with Red Hat to download and build container images. Add your Red Hat Developer Program username and password to your environment to avoid the need to enter it each time you start minishift.

The following commands will set the credentials in the environment of the running shell. When a new command shell is started you will need to enter these commands again.

For cmd.exe
C:\> set MINISHIFT_USERNAME <RED_HAT_USERNAME>
C:\> set MINISHIFT_PASSWORD <RED_HAT_PASSWORD>
For PowerShell
PS> $env:MINISHIFT_USERNAME = '<RED_HAT_USERNAME>'
PS> $env:MINISHIFT_PASSWORD = '<RED_HAT_PASSWORD>'
 

Optional: Permanently storing the credentials
You can permanently store environment variables in the registry using the setx command or Windows Control Panel. After that, when you start a new command shell the environment variables will automatically be set. Note: the setx command will not change the running environment. You will need to start a new command shell for the change to take effect. The syntax is the same for either cmd.exe or PowerShell.

C:\> setx MINISHIFT_USERNAME “<RED_HAT_USERNAME>"
C:\> setx MINISHIFT_PASSWORD “<RED_HAT_PASSWORD>”
3. Build your first app   10 minutes
Start CDK/minishift
You are now ready to start CDK. The minishift start command will start a Red Hat Enterprise Linux VM with an OpenShift cluster running inside.

Note: By this point you should have your hypervisor configured as per the hypervisor prerequisites in step 1:

If you are using Hyper-V, make sure you've created a virtual switch and set the HYPERV_VIRTUAL_SWITCH environment variable.
If you are using VirtualBox, configure minishift to use VirtualBox with minishift config set vm-driver virtualbox
You should have also completed the CDK setup from step 2.

Run minishift start
C:\> minishift start
-- Starting profile 'minishift'
...
-- Minishift VM will be configured with ...
   Memory:    4 GB
   vCPUs :    2
   Disk size: 20 GB
-- Starting Minishift VM .......................... OK
-- Registering machine using subscription-manager
   Registration in progress ..................... OK [42s]
...
Extracting
Image pull complete
OpenShift server started.

The server is accessible via web console at:
    https://192.168.42.60:8443

You are logged in as:
    User:     developer
    Password: 

To login as administrator:
    oc login -u system:admin
...

 

After minishift start completes, make a note of:

The URL for the OpenShift cluster. Note: the IP address may change when you restart CDK.
The developer username.
the admin username and default password.
To check that everything is working and view the OpenShift console use the command:

C:\> minishift console
 

This will launch the OpenShift web console (for example: https://192.168.42.60:8443) with your default browser.  You will likely get a security warning from the browser about the certificate being from an unknown certificate authority. This is expected. You will need to temporarily accept the certificate to proceed.

Note: If you prefer to use a different browser, you can get the URL with the following command:

$ minishift console --url
 

Login to OpenShift using developer as the username.  Enter any text for the developer password.

Add the directory containing oc.exe to your PATH
After the CDK/minishift VM has been started, add oc to your path:

Note: The oc command must match the version of the OpenShift cluster that is running inside of the Red Hat VM. The following commands will set the correct version dynamically by running minishift oc-env and having your shell parse the output.

For cmd.exe:

C:\> @FOR /f "tokens=*" %i IN ('minishift oc-env') DO @call %i
 

For PowerShell:

PS C:\> & minishift oc-env | Invoke-Expression
 

Stopping CDK/minishift and the CDK life-cycle
You can stop the CDK/minishift VM with the command:

C:\> minishift stop
 

You can restart it again with:

C:\> minishift start
 

If necessary, you can delete the VM in order to get a fresh start with a clean VM using:

C:\> minishift delete
 

You won't need to run minishift setup-cdk again unless you delete the contents of %USERPROFILE%/.minishift. You can learn more in the CDK life-cycle section of the CDK Getting Started Guide.

Build your first app
Follow these instructions to create and deploy a Node.js "Hello, World" application on OpenShift running inside the CDK using the OpenShift Web Console.

 

Where to go next?
Learn more about CDK from the CDK Getting Started Guide
If you are new to OpenShift, try the online tutorials at learn.openshift.com
Read the OpenShift documentation
Follow the Red Hat Developers Blog for articles on OpenShift, CDK, containers, and many other topics.
 
