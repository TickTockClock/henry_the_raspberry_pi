# Get your Raspberry Pi Volksbot ready

## Equipment and Setup

The required equipment consists of:
* Raspberry Pi b+
* SD Card 16 GB
* 5 inch touchscreen

The ultimate goal is to publish a C# written program on a Windows computer and eventually execute it on the Raspberry Pi. Therefore you have to consider the following steps:
1. Setup Raspberry Pi
2. Publish the program
3. Transfer publication to Raspberry Pi
4. Run the publication

## 1. Setup Raspberry Pi
* Before installing a operating system on the Raspberry Pi you have to format your SD Card to FAT32.
* Then you will have to download the [Raspbian image](https://www.raspberrypi.org/downloads/raspbian/) on a computer and write the image onto your SD Card. You can use tools like [Win32 Disk Imager](https://www.heise.de/download/product/win32-disk-imager-92033) for doing so.
* Also you would have to download the driver for the touchscreen display, unzip it and copy it onto the SD Card. In this case use [this driver from Joy-It](https://joyiteurope-my.sharepoint.com/personal/onedrive_joyiteurope_onmicrosoft_com/_layouts/15/onedrive.aspx?id=%2Fpersonal%2Fonedrive%5Fjoyiteurope%5Fonmicrosoft%5Fcom%2FDocuments%2F5display%2Fjoy%2DIT%2Dlcd5%2Ddriver%2Etar%2Egz&parent=%2Fpersonal%2Fonedrive%5Fjoyiteurope%5Fonmicrosoft%5Fcom%2FDocuments%2F5display&slrid=89bfb29e-308e-7000-a2fc-36a094d7a491)

Now you want to insert the file containing SD Card into your Raspberry Pi and boot it up.
For more precise installation instructions, please refer to this [Raspberry PI installation guide](https://thepi.io/how-to-install-raspbian-on-the-raspberry-pi/). In general you want to ensure that WiFi, SSH and VNC is being enabled during or after the installation process.  



#### SSH
On Raspbian SSH is disabled by default. In case you cannot enable SSH due to a missing external monitor, you want to create an empty file named `ssh` , without a suffix, into the `/boot/` folder which was created on the SD Card after the Raspbian installation (`/boot/shh`) in order to enable ssh on your Pi.

Also it makes sense to install a bash shell onto your Windows machine, to make communication between your Windows machine and your Pi much easier. Here it is recommend to install [Git for Windows](https://gitforwindows.org/).

***Given that the Raspberry Pi and your local Computer are within the same WiFi network***, you can now log into you Pi via SSH. Open a bash shell and type:
```bash
# e.g. 
ssh pi@192.168.0.1
# and enter your password after which is 'raspberry' by default.
```

On the Raspberry Pi you can open a new terminal and enter the following to open the configuration file where you can change all kinds of things like WiFi, SSH, VNC, Resolution and so on:
```bash
sudo raspi-config
```


With WiFi, SSH and VNC  enabled you can do the following:
1. Connect to your Pi via SSH. Open a bash shell and type:
```bash
# e.g. 
ssh pi@192.168.0.1
# and enter your password after which is 'raspberry' by default.
```
2. You can mirror your Raspberry Pi desktop onto your Windows machine using a tool like [RealVNC](https://www.realvnc.com/en/).
3. You can transfer files between machines with the following bash commands:
```bash
scp -r(recursive) [folder from host 1] [to folder from host 2]

# e.g. send/copy project_henry from windows to the projects folder of the Raspberry Pi. 
scp -r ~/code/project_henry pi@192.169.0.1:~/code/projects 
```

### Joy-It driver
In order to make the LCD display work you have to go into the directory where the driver is and execute the following: 
```bash
sudo bash ./LCD-show
# and reboot after.
```


### Install .NET Core Runtime on Raspberry Pi
1. Install three necessary packages by typing:
```bash
sudo apt-get install curl libunwind8 gettext
```
2. Now you need to download the .NET Core Runtime for ARM32:
```bash
curl -sSL -o dotnet.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/Runtime/release/2.0.0/dotnet-runtime-latest-linux-arm.tar.gz
```
3. Create the destination folder and extract the downloaded package into it.
```bash 
sudo mkdir -p /opt/dotnet && sudo tar zxf dotnet.tar.gz -C /opt/dotnet
```
4. Now set up a symbolic link:
```bash
sudo ln -s /opt/dotnet/dotnet /usr/local/bin
# and test the installation
dotnet --help
```
The in-depth guide can be found by clicking [Setting up Raspian and .NET Core 2.0 on a Raspberry Pi](https://blogs.msdn.microsoft.com/david/2017/07/20/setting_up_raspian_and_dotnet_core_2_0_on_a_raspberry_pi/).

## 2. Publish the program

In order to run a C# application on a Linux machine you have to [publish the program into a Linux readable application](https://github.com/dotnet/core/blob/master/samples/RaspberryPiInstructions.md).
In your Windows DOS Shell you want to navigate into your .NET project and run the following:

```bat
dotnet publish -r linux-arm
```

This will create the self contained app that you will need to copy to your Raspberry Pi under `./bin/Debug/netcoreapp2.1/linux-arm/publish` or `.\bin\Debug\netcoreapp2.1\linux-arm\publish`.


## 3. Transfer publication to Raspberry Pi
You can copy the publication via [WinSCP](https://winscp.net/eng/index.php) or bash:

```bash
scp -r [dir or file host1] [dir host2]
# e.g.
scp -r bin/Debug/netcoreapp2.1/linux-arm/publish pi@192.168.0.2:~/code/application1/
```


## 4. Run the publication
After copying your app, i.e. the whole `publish` directory mentioned above, to the Raspberry Pi `cd publish` and execute `./henry` for example.
Make sure you `chmod 755 ./henry` before execution.

