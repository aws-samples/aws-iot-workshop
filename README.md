# AWS Internet of Things Workshop

## Table of Contents
[Introduction](https://github.com/aws-samples/aws-iot-workshop#introduction)  
[Pre-requisites](https://github.com/aws-samples/aws-iot-workshop#pre-requisites)  
[Workshop Architecture](https://github.com/aws-samples/aws-iot-workshop#workshop-architecture)  
[Lab 0 - Environment setup](https://github.com/aws-samples/aws-iot-workshop#lab-0-this-environment-doesnt-seem-that-hostile)  
[Lab 1 - Get the LED to blink](https://github.com/aws-samples/aws-iot-workshop#lab-1--i-think-your-blinker-is-on)  
[Lab 2 - Connecting to the AWS IoT Core](https://github.com/aws-samples/aws-iot-workshop#lab-2-shadow-dancer)   
[Lab 3 - SMS/Email Temperature Button](https://github.com/aws-samples/aws-iot-workshop#lab-3-some-like-it-hot)  
[Conclusion](https://github.com/aws-samples/aws-iot-workshop#conclusion)  
[References](https://github.com/aws-samples/aws-iot-workshop#references)  

## Introduction  

Hey there inquisitive mind! I heard you're interested in the wondrous Internet of Things (IoT). That's great news cause you're about to get some hands-on experience with IoT using Amazon Web Services. I mean that's pretty cool and all but guess what's gonna make it even better? Well, you'll be using an actual honest-to-goodness physical IoT micro-controller board! Damn, I wish I was you. Getting all ready to dive head first into this exciting and dynamic field. But before I digress too much...let's get this party started.

## Pre-requisites
1. PC/Mac with Administrator access
2. Valid AWS Account
3. Internet access
4. An ESP32 device board such as [this](https://www.amazon.com/gp/product/B0718T232Z/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1)
5. A micro-USB cable such as [this](https://www.amazon.com/AmazonBasics-Male-Micro-Cable-Black/dp/B0711PVX6Z/ref=sr_1_5?ie=UTF8&qid=1515622065&sr=8-5&keywords=micro+usb+cable)

## Workshop Architecture

<p align="center"> 
<img src="images/WorkshopMainImage.png" width="80%">

</p>


## Lab 0: This environment doesn't seem that hostile

First up, we're gonna setup our development environment. We'll be using a piece of software called an Integrated Development Environment (IDE). This software will facilitate compiling, executing and debugging code directly on our ESP32 device. 

Our IDE of choice is from Arduino and is funnily enough called the Arduino IDE. You create a Sketch file (.ino) and then author your code within. The programming language is C/C++ based as Arduino is a collection of C/C++ functions. These can be called from your code and under the covers Arduino passes it onto the C/C++ compiler, which translates it accordingly. 

Choose your destiny and click the relevant OS you're running to begin installation: [macOS](#macos) OR [Windows](#windows).

#### macOS

### Step 1:

Download the latest MacOS version from https://www.arduino.cc/en/software

### Step 2:
Unzip arduino-*-macosx.zip which will extract a file called Arduino.app. 

<p align="center"> 
<img src="images/ar_zip.png" width="13%">
</p>

>**Note**: A .app file which is actually a specially encoded Unix directory. This serves as an application bundle and contains all the necessary Apple-specific files which encompass a runnable application.

Double-click the .app file to start the IDE.

<p align="center"> 
<img src="images/app.png" width="13%">
</p>

### Step 3: 
Navigate to **Tools -> Board**. As you can see, all the standard Arduino boards are listed for selection such as Uno, Nano, Mega, etc. 

<p align="center"> 
<img src="images/ard_std.png" width="55%">
</p>

The ESP32 is a non-standard board thus we need to install a 3rd-party library for it work. This can be done in an automatic or manual fashion. In this case, we will opt for a semi-automatic installation by cloning a Github repository into an Arduino-specific folder.

Open a bash shell by pressing **"Command + Space"** and then typing "**terminal**". Press "Enter". 

<p align="center"> 
<img src="images/driver6.png" width="55%">
</p>

Paste the following and execute it in your terminal: 

```shell
mkdir -p ~/Documents/Arduino/hardware/espressif && \
cd ~/Documents/Arduino/hardware/espressif && \
git clone https://github.com/espressif/arduino-esp32.git esp32 && \
cd esp32 && \
git submodule update --init --recursive && \
cd tools && \
python get.py
```

The output should look as follows.

<p align="center"> 
<img src="images/lib1.png" width="65%">
</p>

>**Troubleshooting:** If you encounter a `certificate verify failed: unable to get local issuer certificate` issue please review https://stackoverflow.com/a/57795811/7412781 

Restart your IDE then navigate back to **"Tools -> Board"**, you'll now see the ESP32 board listed.

<p align="center"> 
<img src="images/esp32.png" width="55%">
</p>

### Step 4: 
Navigate to **"Sketch -> Include Library"**. You'll see that the standard Arduino libraries are available for inclusion.

<p align="center"> 
<img src="images/libraries.png" width="55%">
</p>

We'll be utilising a custom Arduino ESP32 library to connect to AWS IoT. This uses the AWS Embedded-C SDK and wraps the relevant IoT functions.

Open a bash shell by pressing **"Command + Space"** and then typing "**terminal**" into the prompt. Press "Enter".

Paste the following and execute it in your terminal: 

```shell
mkdir -p ~/Documents/Arduino/tempDir && \
cd ~/Documents/Arduino/tempDir && \
git clone https://github.com/ExploreEmbedded/Hornbill-Examples.git && \
cd Hornbill-Examples/arduino-esp32 && \
mv AWS_IOT ~/Documents/Arduino/libraries && \
rm -rf ~/Documents/Arduino/tempDir
```

The output should look as follows.

<p align="center"> 
<img src="images/lib1.png" width="65%">
</p>

Restart your IDE then go back to **"Sketch -> Include Library"**, you'll see that the AWS IOT library appears now.

<p align="center"> 
<img src="images/awsiot.png" width="55%">
</p>

Close your IDE.

### Step 5:
Before we plug your ESP32 device board in, we need to install drivers so that your OS knows how to communicate with it. 

Download this file: https://www.silabs.com/documents/public/software/Mac_OSX_VCP_Driver.zip and extract it.

<p align="center"> 
<img src="images/driver2.png" width="13%">
</p>

Double-click the extracted dmg file and follow the prompts to mount the drivers image

<p align="center"> 
<img src="images/driver2.1.png" width="55%">
</p>

Once the image is mounted, double-click the .pkg file.

<p align="center"> 
<img src="images/driver3.png" width="55%">
</p>

Click "Continue" and follow the rest of the prompts to install the drivers.

<p align="center"> 
<img src="images/driver4.png" width="55%">
</p>

Once the drivers have been installed, open a bash shell by pressing **"Command + Space"** and then typing "**terminal**". Press "Enter". 

<p align="center"> 
<img src="images/driver6.png" width="55%">
</p>

Plug your ESP32 board into a USB port and then type **"ls /dev/tty.*"**. Press "Enter".

<p align="center"> 
<img src="images/driver5.png" width="65%">
</p>

As you can see, the ESP32 device is now configured and picked up by your OS as **"/dev/tty.SLAB_USBtoUART"**.

>**Troubleshooting:** If you cannot see your device please follow the steps under this link - https://github.com/espressif/arduino-esp32/issues/1084#issuecomment-425542458  

### Step 6:
Open up your Arduino IDE and navigate to **"Tools"**. Click **"Board"** and select **"ESP32 Dev Module"**.  

Ensure the settings are as follows:

Board: **"ESP32 Dev Module"**
Flash Mode: **"QIO"**
Flash Frequency: **"80MHz"**
Flash Size: **"4MB (32MB)"**
Upload Speed: **"921600"**
Core Debug Level: **"None"**

<p align="center"> 
<img src="images/esp32 board info.png" width="35%">
</p>

For Port, select the one related to the previous step - **"SLAB_USBtoUART"**

<p align="center"> 
<img src="images/board2.png" width="55%">
</p>

Lastly, we'll utilise the built-in Serial Monitor to view/debug output of print statements such as **Serial.printf()**.

Navigate to **"Tools -> Serial Monitor"**

<p align="center"> 
<img src="images/serial0.png" width="55%">
</p>

Change the baud rate to **115200**

>**Note:** There is no notification once you change the rate so proceed accordingly.

<p align="center"> 
<img src="images/serial.png" width="55%">
</p>

You're now ready to start using your ESP32 device!

#### Windows

### Step 1:

Download and install the latest Windows version from https://www.arduino.cc/en/software

Once installation is complete, start the IDE.

>**Note:** The installation path for Sketches should default to C:\Users\username\Documents\Arduino where  username is the one you use to log into your PC

### Step 2: 
Navigate to **"Tools -> Board"**. As you can see, all the standard Arduino boards are listed for selection such as Uno, Nano, Mega, etc. 

<p align="center"> 
<img src="images/win2.png" width="55%">
</p>

The ESP32 is a non-standard board and thus we need to install a 3rd-party library for it work. This can be done in an automatic or manual fashion. In this case, we will opt for a semi-automatic installation by cloning a Github repository into an Arduino-specific folder.

Since Windows doesn't come with Git, proceed with installing it from this url: https://git-scm.com/download/win

>**Note:** Use the default installation options

Once installation is complete, click the Start menu and open "Git CMD".

<p align="center"> 
<img src="images/win_git0.png" width="35%">
</p>

Paste the following and execute it in the window: 

```shell
C: && mkdir %userprofile%\Documents\Arduino\hardware\espressif && cd %userprofile%\Documents\Arduino\hardware\espressif && git clone https://github.com/espressif/arduino-esp32.git esp32 && cd esp32 && git submodule update --init --recursive
```

The output should look as follows.

<p align="center"> 
<img src="images/win_git1.png" width="85%">
</p>

Click the Start menu, type ""**CMD**" and press enter to open a regular Command Prompt.

Paste the following and execute it in the window: 

```shell
C: && cd %userprofile%\Documents\Arduino\hardware\espressif\esp32\tools\ && get.exe
```

The output should look as follows.

<p align="center"> 
<img src="images/win_git3.png" width="65%">
</p>

Restart your IDE then navigate back to **"Tools -> Board"**, you'll now see the ESP32 board listed.

<p align="center"> 
<img src="images/win3.png" width="55%">
</p>

### Step 3: 
Navigate to **"Sketch -> Include Library"**. You'll see that the standard Arduino libraries are available for inclusion.

<p align="center"> 
<img src="images/win4.png" width="55%">
</p>

We'll be utilising a custom Arduino ESP32 library to connect to AWS IoT. This library itself makes use of the AWS Embedded-C SDK and wraps the IoT functions.

Click the Start menu and open "Git CMD".

<p align="center"> 
<img src="images/win_git00.png" width="45%">
</p>

Paste the following and execute it in the Git CMD window: 

```shell
C: && mkdir %userprofile%\Documents\Arduino\tempDir && cd %userprofile%\Documents\Arduino\tempDir && git clone https://github.com/ExploreEmbedded/Hornbill-Examples.git && cd Hornbill-Examples\arduino-esp32 && move AWS_IOT %userprofile%\Documents\Arduino\libraries && cd \ && rmdir /s /q %userprofile%\Documents\Arduino\tempDir
```

The output should look as follows.

<p align="center"> 
<img src="images/win_git2.png" width="85%">
</p>

Restart your IDE then go back to **"Sketch -> Include Library"**, you'll see that the AWS IOT library appears now.

<p align="center"> 
<img src="images/win5.png" width="45%">
</p>

Close your IDE.

### Step 4:
For Windows to recognise the ESP32 device we need to install applicable drivers. 

Download and install the drivers from this location: https://www.silabs.com/documents/public/software/CP210x_Universal_Windows_Driver.zip

>**Note:** The installation file is usually named CP210xVCPInstaller_x64.exe

Once you've completed the driver installation, plug your ESP32 into a USB port.

### Step 5:
Open up your Arduino IDE and navigate to **"Tools"**. Click **"Board"** and select **"ESP32 Dev Module"**.  

Ensure the settings are as follows:

Board: **"ESP32 Dev Module"**
Flash Mode: **"QIO"**
Flash Frequency: **"80MHz"**
Flash Size: **"4MB (32MB)"**
Upload Speed: **"921600"**
Core Debug Level: **"None"**

<p align="center"> 
<img src="images/port0.png" width="35%">
</p>

Click the Start menu, type "**Device Manager**" and press Enter.

Scroll down to Ports and expand the dropdown. Make a note of the COM port associated with Silicon labs.

<p align="center"> 
<img src="images/win_git4.png" width="55%">
</p>

In your Arduino IDE, select the matching COM Port.

<p align="center"> 
<img src="images/port1.png" width="35%">
</p>

Lastly, we'll utilise the built-in Serial Monitor to view/debug output of print statements such as **Serial.printf()**.

Navigate to **"Tools -> Serial Monitor"**

<p align="center"> 
<img src="images/win8.png" width="35%">
</p>

Change the baud rate to **115200**.

>**Note:** There is no notification once you change the rate so proceed accordingly.

<p align="center"> 
<img src="images/win9.png" width="55%">
</p>

You're now ready to start using your ESP32 device!

### What did we learn?

- How to configure your IDE on macOS or Windows
- How to install 3rd party libraries into your IDE
- How to configure your ESP32 in your IDE
- How to install device drivers for your ESP32

## Lab 1 : I think your blinker is on

### Architecture for this lab

<p align="center"> 
<img src="images/Lab1.png" width="80%">
</p>

In this lab we're gonna start small and get that fancy pants built-in blue LED to turn on and off every 5 seconds. Kinda like the blinker in your car except you'll actually use it!  

### Step 1:   
Create a new Sketch by clicking **"File -> New"**. It will look similar to the code fragment below:

```c
void setup() {
  // Setup code which will execute once
}

void loop() {
  // Application code which will run infinitely
}
```

These two functions/methods drive everything in your application. The setup method is used for initial setup of your application such as initialising variable, setting up pins, etc. The loop method is called indefinitely after the setup method has been executed.

Copy the following code into your Sketch.

```c
const int ledPin = 2;

void setup() {
  pinMode (ledPin, OUTPUT);
}

void loop() {
  digitalWrite (ledPin, HIGH);
  delay(5000);
  digitalWrite (ledPin, LOW);
  delay(5000);
}
```

Let's examine it at a high-level before we execute it.

**=====Code Explanation Start=====**

```c
const int ledPin = 2;
```

This piece creates a constant integer for the LED pin mapping and sets it to 2. Think of a constant as a fixed variable which we can reference throughout our sketch.

```c
pinMode (ledPin, OUTPUT);
```

This sets the ledPin to the **"OUTPUT"** mode which will allow us to turn the LED on and off with further commands. 

```c
digitalWrite (ledPin, HIGH);
```

This tells the code to do a write to the ledPin with the value of HIGH. This will turn the LED on.

```c
delay(5000);
```

This tells the code to delay execution by 5000 milliseconds or 5s.

```c
digitalWrite (ledPin, LOW);
```

This tells the code to do a write to the pin (ledPin) with value of LOW. This will turn the LED off.

**=====Code Explanation End=====**

### Step 2:

Before we execute our code, it needs to be checked for any errors like using the wrong syntax or function. To do this it needs to be compiled so click **"Sketch -> Verify/Compile"** or press **"Command + R"** (macOS) ||| **Ctrl + R"** (Windows). 

<p align="center"> 
<img src="images/compile.png" width="35%">
</p>

This will start compilation which you can see near the bottom of your window along the status bar.

<p align="center"> 
<img src="images/compiling.png" width="75%">
</p>

Once our code is compiled you should see **"Done compiling"** show up in the status bar as follows:

<p align="center"> 
<img src="images/compiling2.png" width="35%">
</p>

### Step 3:

The next part is uploading the code to the device. We will essentially flash or overwrite the device's current firmware with your code. Click **"Sketch -> Upload"** or press **"Command + U"** (macOS) ||| **Ctrl + U"** (Windows). 

<p align="center"> 
<img src="images/upload.png" width="35%">
</p>

This will start uploading and update your status bar accordingly.

<p align="center"> 
<img src="images/uploading.png" width="25%">
</p>

Once our code is uploaded you should see **"Done uploading"** in the status bar. 

<p align="center"> 
<img src="images/uploaded.png" width="25%">
</p>

Your device will then reset and you should see the LED start to alternates between on and off every 5 seconds.

Blue LED - ON

<p align="center"> 
<img src="images/on.jpeg" width="35%">
</p>

Blue LED - OFF

<p align="center"> 
<img src="images/off.jpeg" width="35%">
</p>

### Step 4:

As a final step, let's add some logging statements so we know when the LED is on or off without looking at the physical device.

Update the setup method as follows:

```c
void setup() {
  pinMode (ledPin, OUTPUT);
  Serial.begin(115200);
}
```

Next, update the loop method as follows:

```c
  void loop() {
  digitalWrite (ledPin, HIGH);
  Serial.println("ON");
  delay(5000);
  digitalWrite (ledPin, LOW);
  Serial.println("OFF");
  delay(5000);
}
```

Before we execute, let's examine the changes.

**=====Code Explanation Start=====**

```c
Serial.begin(115200);
```

This line sets up communication between our macOS and ESP32 device.

```c
Serial.println("ON");
```

This outputs **"ON"** to the serial port.

```c
Serial.println("OFF");
```

This outputs **"OFF"** to the serial port.

**=====Code Explanation End=====**

Compile and upload your code by pressing **"Command + U"** (macOS) ||| **Ctrl + U"** (Windows). Once your device has restarted open up the Serial Monitor by pressing **"Command + SHIFT + M""** (macOS) ||| **Ctrl + SHIFT + M"** (Windows). You should now see the output of both println() statements as the LED turns on and off.

>**Troubleshooting:** If you device seems to be stuck at the connecting part, hold down the button to the right of the USB cable (if you're holding it like a lollipop). This will allow it to be flashed with your code.  

<p align="center"> 
<img src="images/offonserial.png" width="35%">
</p>

You've now completed your first Arduino Sketch. Feel free to change the timing to make it blink faster or slower. Once you're done, move onto the next lab, slugger.

### What did we learn?

- How to write your first Sketch
- How to compile code and upload to your ESP32
- How to interact with an LED on your ESP32
- How to output to the Serial Monitor

## Lab 2: Shadow Dancer

### Architecture for this lab

<p align="center"> 
<img src="images/Lab2.png" width="80%">
</p>

In this lab, we're gonna connect our ESP32 device to the AWS IoT Core as a "thing" and then update it's thing shadow.

>**Note:** A thing is a representation of an IoT device or logical entity such as an application. In this case it will be our ESP32 device. Furthermore, a thing/device shadow is a JSON-formatted document that stores the last reported state for a given thing/device/app, etc.

### Step 1: Create a policy

For our ESP32 device to access AWS Services (once in the AWS Cloud) it needs to utilise a policy. 

>**Note:** A policy is a JSON-formatted document that allows/denies access to AWS Services. For our device to interact with AWS IoT we need to create a relevant policy.

Log into your AWS account and search for "IoT" under **AWS services**. You can then click the applicable result for **AWS IoT**.

<p align="center"> 
<img src="images/iot1.png" width="35%">
</p>

Make sure you are in a region which supports AWS IoT like Ohio. If you click the dropdown in the top-right corner, you can select the appropriate region.

<p align="center"> 
<img src="images/region.png" width="35%">
</p>

Navigate to **"Secure -> Policies"** from the left navigation then click on the **"Create"** button.

<p align="center"> 
<img src="images/policy.png" width="100%">
</p>

Name the policy **"thingamajig_policy"**.

<p align="center"> 
<img src="images/policy_name.png" width="35%">
</p>

Scroll down to the **"Add statements"** section and fill in the fields as follows:

Action: iot:*  
Resource ARN: *  
Effect: **Allow** (select the checkbox)

Once you're done, click **"Create"** to complete policy creation.

<p align="center"> 
<img src="images/policy_statement.png" width="65%">
</p>

### Step 2: Create a thing

Navigate to **"Manage -> Things"** from the left navigation then click on the **"Create"** button.

<p align="center"> 
<img src="images/iot2.png" width="100%">
</p>

Click on the **"Create a single thing"** button.

<p align="center"> 
<img src="images/iot3.png" width="100%">
</p>

Name the device **"Thingamajig"** then scroll down to the bottom and click **"Next"**.

> **Note:** You can leave the rest of the settings as default.

<p align="center"> 
<img src="images/iot4.png" width="55%">
</p>

We'll be using certificate-based authentication so our device can connect to the AWS IoT Core in a secure manner.

Click **"Create certificate"**

<p align="center"> 
<img src="images/iot6.png" width="100%">
</p>

Download all four documents (two certificates and two keys) then click the **"Activate"** button.

<p align="center"> 
<img src="images/iot7.png" width="85%">
</p>

>**Note:** The Deactivate button appears once you have activated the certificates.

Next up we'll attach the policy from the previous step. Scroll down to the bottom of the screen and click **"Attach a policy"**. 

<p align="center"> 
<img src="images/attach.png" width="40%">
</p>

Search for the thingamajig policy by scrolling or input **"thingamajig"** into the search box. Once you locate the policy, select the checkbox next to it and click **"Register Thing"**.

<p align="center"> 
<img src="images/policy_reg.png" width="100%">
</p>

Once your thing has been created you'll be taken back to the Things page where it will appear accordingly:

<p align="center"> 
<img src="images/thing_created.png" width="85%">
</p>

### Step 3: Deploying the certificates and private key to your device

We need to configure the device itself so it can connect to the AWS IoT Core.

Locate this file under the AWS_IOT library that we installed in the first lab and open it with a Text editor: **aws_iot_certificates.c**. You'll see three arrays which we need to edit.

macOS: **~/Documents/Arduino/libraries/AWS_IOT/src/**  
Windows: **C:\Users\username\Documents\Arduino\libraries\AWS_IOT\src** where username is your Windows login.

<p align="center"> 
<img src="images/certs_array.png" width="55%">
</p>

We'll insert the file contents from our downloaded documents into the arrays with the following mappings:

Filename | Array name | Description
--- | --- | ---
AmazonRootCA1.pem | **aws_root_ca_pem** | Root certificate
xxxxxxxxxxx-certificate.pem.crt | **certificate_pem_crt** | Thing certificate
xxxxxxxxxxx-private.pem.key | **private_pem_key** | Private Key

Open the **Root certificate** with your text editor.

<p align="center"> 
<img src="images/root_cert.png" width="85%">
</p>

It will look similar to following extract (albeit with more lines):

```text
-----BEGIN CERTIFICATE-----
...
MIIE0zCCA7ugAwIBAgIQGNrRniZ96LtKIVjNzGs7SjANBgkqhkiG9w0BAQUFADCB
aXR5IC0gRzUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCvJAgIKXo1
hnacRHr2lVz2XTIIM6RUthg/aFzyQkqFOFSDX9HoLPKsEdao7WNq
...
-----END CERTIFICATE-----
```

Paste the file contents into the **aws_root_ca_pem** array in the following fashion. Make sure you follow these four rules when doing so:

1. Do not add any extra whitespaces 
2. End your lines with: \n\
3. End the last line with: \n
4. Put the array in quotes: ""

```c
const char aws_root_ca_pem[] = {"-----BEGIN CERTIFICATE-----\n\
MIIE0zCCA7ugAwIBAgIQGNrRniZ96LtKIVjNzGs7SjANBgkqhkiG9w0BAQUFADCB\n\
aXR5IC0gRzUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCvJAgIKXo1\n\
hnacRHr2lVz2XTIIM6RUthg/aFzyQkqFOFSDX9HoLPKsEdao7WNq\n\
-----END CERTIFICATE-----\n"};

```

>**Note:** It's imperative that you follow the four rules as many people get caught out by this copy and paste effort.

We'll repeat the process for the **Thing certificate** so open that up with your text editor.

<p align="center"> 
<img src="images/cert_cert.png" width="85%">
</p>

It will look similar to following extract (albeit with more lines):

```text
-----BEGIN CERTIFICATE-----
...
UyXRLsYwcMR4rs3Bq9G/BGU4WXRHWOJ00UjBeqaCh8PF9Y0jPsomfOTc6p8NwU73
SccQ4UVmzJWPHzNNngKgm28WkyBhrWub7RdE8/JBOvitUrnC6j+hD2XmKweZV+v6
mY6oMQITy+QyWsqHxpLcd0HGe75xfJ3XnT+spEywADj6VpemBOXnpu9kDcs4
...
-----END CERTIFICATE-----
```

Paste the file contents into the **certificate_pem_crt** array in the following fashion.  Make sure you follow these four rules when doing so:

1. Do not add any extra whitespaces 
2. End your lines with: \n\
3. End the last line with: \n
4. Put the array in quotes: ""

```c
const char certificate_pem_crt[] = {"-----BEGIN CERTIFICATE-----\n\
UyXRLsYwcMR4rs3Bq9G/BGU4WXRHWOJ00UjBeqaCh8PF9Y0jPsomfOTc6p8NwU73\n\
SccQ4UVmzJWPHzNNngKgm28WkyBhrWub7RdE8/JBOvitUrnC6j+hD2XmKweZV+v6\n\
mY6oMQITy+QyWsqHxpLcd0HGe75xfJ3XnT+spEywADj6VpemBOXnpu9kDcs4\n\
-----END CERTIFICATE-----\n"};

```

>**Note:** It's imperative that you follow the four rules as many people get caught out by this copy and paste effort.

Lastly, we'll copy over the **Private key** so open that up with your text editor.

<p align="center"> 
<img src="images/priv_cert.png" width="85%">
</p>

It will look similar to following extract (albeit with more lines):

```text
-----BEGIN RSA PRIVATE KEY-----
...
GpLmWQKBgFZVdyV74fWKxcrCFSVGqQkiR6C97w+LjfBd3NZbGym1oA9yvqDKnWCt
qsB1OUSoKd7tZImu2UQSrGBnP472ENfjcTYAEq6EoUUFeWfZ6SvscQlrVKWtEiEj
r1jg7VZIHbQ46Ecejv5TMCbcDJZcPR7B00W333cHLeV62GPSNISn
...
-----END RSA PRIVATE KEY-----
```

Paste the file contents into the **private_pem_key** array in the following fashion.  Make sure you follow these four rules when doing so:

1. Do not add any extra whitespaces 
2. End your lines with: \n\
3. End the last line with: \n
4. Put the array in quotes: ""

```c
const char private_pem_key[] = {"-----BEGIN RSA PRIVATE KEY-----\n\
GpLmWQKBgFZVdyV74fWKxcrCFSVGqQkiR6C97w+LjfBd3NZbGym1oA9yvqDKnWCt\n\
qsB1OUSoKd7tZImu2UQSrGBnP472ENfjcTYAEq6EoUUFeWfZ6SvscQlrVKWtEiEj\n\
r1jg7VZIHbQ46Ecejv5TMCbcDJZcPR7B00W333cHLeV62GPSNISn\n\
-----END RSA PRIVATE KEY-----\n"};

```

>**Note:** It's imperative that you follow the four rules as many people get caught out by this copy and paste effort.

Once you've copied over all of the files, save the **aws_iot_certificate.c** file.

### Step 4: Creating the application and testing

Create a new Sketch by clicking **"File -> New"**. We're gonna paste three code fragments to build up our application.

To start, paste the following above the setup() method:

```c
// This include is for the AWS IOT library that we installed
#include <AWS_IOT.h>
// This include is for Wifi functionality
#include <WiFi.h>

// Declare an instance of the AWS IOT library
AWS_IOT hornbill;

// Wifi credentials
char WIFI_SSID[]="";
char WIFI_PASSWORD[]="";

// Thing details
char HOST_ADDRESS[]="";
char CLIENT_ID[]= "Thingamajig";
char TOPIC_NAME[]= "$aws/things/Thingamajig/shadow/update";

// Connection status
int status = WL_IDLE_STATUS;
// Payload array to store thing shadow JSON document
char payload[512];
// Counter for iteration
int counter = 0;
```

Update our wifi credentials with the relevant ones at these lines:

```c
char WIFI_SSID[]="xxxxxxxxx";
char WIFI_PASSWORD[]="xxxxxxxx";
```

Navigate to **"Manage -> Things"** from the **AWS IoT** left navigation then click on the **"Thingamajig"** thing.

<p align="center"> 
<img src="images/thing0.png" width="55%">
</p>

Click **Interact** from the left navigation and copy the HTTPS URL.

<p align="center"> 
<img src="images/thing1.png" width="55%">
</p>

Update our host address in this section with the copied URL:

```c
char HOST_ADDRESS[]="xxxxxxx";
char CLIENT_ID[]= "Thingamajig";
char TOPIC_NAME[]= "$aws/things/Thingamajig/shadow/update";
```

>**Note:** If you named your device/thing something difference than "Thingamajig", please make sure to update the client id and topic name too. 

Replace the setup() method with the following code:

```c
void setup()
{
  WiFi.disconnect(true);
  Serial.begin(115200);
  // initialise AWS connection
  while (status != WL_CONNECTED) {
    Serial.print("Attempting to connect to Wifi network: ");
    Serial.println(WIFI_SSID);
    status = WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
    delay(5000);
  }
  Serial.println("Connected to Wifi!");
  if(hornbill.connect(HOST_ADDRESS,CLIENT_ID)== 0) {
    Serial.println("Connected to AWS, bru");
    delay(1000);
  }
  else {
    Serial.println("AWS connection failed, Check the HOST Address");
    while(1);
  }
}
```

If you step through the new code, you can see that we are first trying to connect to our Wifi network:

**=====Code Explanation Start=====**

```c
status = WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
```

This is followed by an attempt to connect to AWS:

```c
hornbill.connect(HOST_ADDRESS,CLIENT_ID)
```

**=====Code Explanation End=====**

Next up we're gonna change the loop() method to perfom the update of our thing shadow. Copy the following code and replace the blank loop() method:

```c
void loop()
{   
  counter++;
  sprintf(payload,"{\"state\":{\"reported\":{\"counter\":\"%d\"}}}",counter);
  Serial.println(payload);
  if(hornbill.publish(TOPIC_NAME,payload) == 0) {
    Serial.println("Message published successfully");
  }
  else {
    Serial.println("Message was not published");
  }
  delay(5000);  
}
```

**=====Code Explanation Start=====**

This line increments our counter:

```c
counter++;
```

This line constructs a JSON-formatted character array with our counter's latest value. It then sets our payload to this resultant array:

```c
sprintf(payload,"{\"state\":{\"reported\":{\"counter\":\"%d\"}}}",counter);
```

This line publishes the payload to our topic that we specified earlier:

```c
hornbill.publish(TOPIC_NAME,payload)
```

**=====Code Explanation End=====**

Compile and upload the Sketch to our ESP32 device.

Navigate to **"Manage -> Things"** from the **AWS IoT** left navigation then click on the **"Thingamajig"** thing.

<p align="center"> 
<img src="images/thing_created.png" width="85%">
</p>

Click **Shadow** from the left navigation and scroll down to **"Shadow state:"**. You'll see the counter will be updated as our application executes.

<p align="center"> 
<img src="images/thing3.png" width="55%">
</p>

Click **Activity** from the left navigation and you'll see updates that have succeeded or failed as they come through.

<p align="center"> 
<img src="images/thing4.png" width="85%">
</p>

Back in Arduino, navigate to **"Tools -> Serial Monitor"** and you'll see the output of our print statements as well. Woah, that's pretty cool. We've successfully connected our ESP32 device to our AWS IoT Core!

>**Troubleshooting:** If your device seems to be stuck connecting to Wifi, unplug it and plug it back in. Then try upload your code again. 

### What did we learn?

- How to create a Thing to map to your ESP32
- How to create a Policy for use by your Thing
- How to create certificates and keys for your Thing
- How to connect to the AWS IoT Core
- How to update your thing/device shadow

## Lab 3: Some like it hot

### Architecture for this lab

<p align="center"> 
<img src="images/WorkshopMainImage.png" width="80%">
</p>

In this lab,  we're gonna simulate temperature fluctuations on our ESP32 (by pressing the on-board button). An email will be sent out if the temperature is below 25 Celsius. However, if the temperature exceeds 25 Celsius then an SMS warning will be sent out instead.

### Step 1: Setting up an SNS topic for Email 

Log into your AWS account and search for "sns" under **AWS services**. You can then click the applicable result for **Simple Notification Service**.

>**Note:** Simple Notification Service is used to send email and SMS (Text) messages. You create an SNS Topic then add subscribers who will be notified when a message is added to the subscribed Topic.

<p align="center"> 
<img src="images/sns.png" width="45%">
</p>

Click **"Create topic"** under the **Common Actions** section.

<p align="center"> 
<img src="images/sns1.png" width="55%">
</p>

Name the Topic **"thingamajig_topic"** and the Display name **"esp32"**. Once you've done that, click **"Create topic"**.

<p align="center"> 
<img src="images/sns2.png" width="100%">
</p>

After the topic has been created you'll see a screen similar to the following:

<p align="center"> 
<img src="images/sns3.png" width="55%">
</p>

Make a note of the Topic ARN as you'll need it in **Step 6**.

### Step 2: Creating a subscription

Click **"Create subscription"**.

<p align="center"> 
<img src="images/sns4.png" width="35%">
</p>

Select **"Email"** from the Protocol dropdown.

<p align="center"> 
<img src="images/sns5.png" width="75%">
</p>

Enter a valid email address in the **"Email"** field then click **"Create subscription"**.

<p align="center"> 
<img src="images/sns6.png" width="75%">
</p>

You'll see the subscription as **"PendingConfirmation"** which means we need to confirm the email address.

<p align="center"> 
<img src="images/sns7.png" width="75%">
</p>

You should receive a confirmation email in the next few minutes. Once you do, open it up and click the **"Confirm subscription"** link.

<p align="center"> 
<img src="images/sns8.png" width="85%">
</p>

This will confirm that the subscription is valid and you'll see the following page open up in your browser:

<p align="center"> 
<img src="images/sns9.png" width="75%">
</p>

Go back to your **Topic** and you'll see that the subscription has been populated. This means the subscription has been confirmed successfully. 

<p align="center"> 
<img src="images/sns10.png" width="75%">
</p>

### Step 3: Testing our subscription

Click **"Publish to topic"**.

<p align="center"> 
<img src="images/sns11.png" width="35%">
</p>

Enter **"test"** as the Subject and something apt for the Message field:

```TEXT
Hey there,

I'm a test message from the AWS SNS topic!

Thanks
AWS IoT Workshop
```

<p align="center"> 
<img src="images/sns12.png" width="55%">
</p>

Scroll down to the bottom of the screen and click **"Publish message"**

<p align="center"> 
<img src="images/sns13.png" width="35%">
</p>

You should receive the test email in the next few minutes.

<p align="center"> 
<img src="images/sns14.png" width="95%">
</p>

### Step 4: Setting up an SNS topic for SMS

Log into your AWS account and switch to the **N.Virgina** region in the top right-hand corner.

>**Note:** SMS (via SNS) is supported in this region and we'll be setting up a cross region call in order to accomplish the sending of our warning message. 

Search for "sns" under **AWS services**. You can then click the applicable result for **Simple Notification Service**.

Create a topic with the following details:

Name: **"testCross"** 
Display name: **"sms_topic"**.

After the topic has been created you'll see a screen similar to the following:

<p align="center"> 
<img src="images/sms1.png" width="55%">
</p>

Make a note of the Topic ARN as you'll need it in **Step 6**.

### Step 5: Creating a subscription

Create a subscription for the newly created Topic with the following details:

Protocol: **SMS**
Endpoint: ** Your cellphone number **

After the subscription has been created you'll see it appear under your topic.

<p align="center"> 
<img src="images/sms2.png" width="75%">
</p>

### Step 6: Create a Lambda Function

Switch back to the **Ohio** region and search for "lambda" under **AWS services**. You can then click the applicable result for **Lambda**.

Create a Lambda function with the following values filled out:

Name: **testCross**
Runtime: **Node.js 6.10**
Existing role: **lambda_basic_execution**

>**Note:** Ensure the lambda_basic_execution role has an attached policy that allows access to SNS. This can be done via the IAM UI.

<p align="center"> 
<img src="images/lambda1.png" width="75%">
</p>

Click "**Create Function**" and then paste the following code into the **Function code** input box.

```Javascript
var AWS = require("aws-sdk");

exports.handler = function(event, context) {
    var eventText = JSON.stringify(event, null, 2);
    console.log("Received event:", eventText);
    var temperature = (JSON.parse(eventText)).state.reported.temperature;
    
    if (temperature > 25) {
        var sns = new AWS.SNS({region: 'us-east-1'});
        var params = {
            Message: "*Temperature Critical*\n" + "Your device temperature is " + temperature + "C", 
            Subject: "Critical Warning",
            TopicArn: "**INSERT SMS TOPIC ARN HERE**"
        };
        
    } else {
        var sns = new AWS.SNS({region: 'us-east-2'});
        var params = {
            Message: "*Temperature Warning*\n Your device temperature is: " + temperature + "C.\n" +
            "Please reduce the temperature of your device to avoid damage.", 
            Subject: "Warning",
            TopicArn: "**INSERT Email TOPIC ARN HERE**"
        }; 
    }
   
   sns.publish(params, context.done);
};
```

Make sure you substitute the relevant Topic ARNs from **Step 1** and **Step 4** into **"INSERT SMS TOPIC ARN HERE"** and **"INSERT Email TOPIC ARN HERE"**

If you want to test your function click the Test dropdown near the top-right and select "**Configure test events**". 

Enter the following as input and click "**Save**".

```JSON
{
    "state": {
      "reported": {
        "temperature": "26"
      }
    }
}
```

Now click the "**Test**" button near the top-right. You should receive an SMS on the number you specified.

### Step 7: Create an IoT Rule

An IoT Rule is used to route device shadow updates to a specified AWS Service. In our case, we are gonna create a rule which will send info to our SNS topic.

Log into your AWS account and search for "IoT" under **AWS services**. You can then click the applicable result for **AWS IoT**. Navigate to **Act** and then click the **"Create"** button on the far right.

<p align="center"> 
<img src="images/rule1.png" width="100%">
</p>

Name the rule **"thingamajig_rule"** then scroll down to the **"Message source"** section.

<p align="center"> 
<img src="images/rule2.png" width="55%">
</p>

Enter * for **"Attribute"** and then input **$aws/things/Thingamajig/shadow/update/accepted** as the **"Topic filter"**. 

>**Note:** We are pulling all values from the device shadow (** * ** ) when there has been a successful update (**$aws/things/Thingamajig/shadow/update/accepted**). As of Nov 2018, you will need to enter the statement directly into the Rule as: SELECT * FROM '$aws/things/Thingamajig/shadow/update/accepted'

<p align="center"> 
<img src="images/rule3.png" width="65%">
</p>

Scroll down and click **"Add action"**. Select the checkbox for the **Lambda** option and then scroll down and click **"Configure action"**. Select the "**testCross**" Lambda function we created in **Step 6** then scroll down and click **"Add action"**.

You'll be taken back to the **IoT Rules** page and will see that our Rule has been created successfully.

<p align="center"> 
<img src="images/rule13.png" width="65%">
</p>

### Step 8: Creating the application and testing it

Create a new Sketch by clicking **"File -> New"**. We're gonna paste five code fragments to build up our application.

To start, paste the following above the **setup()** method:

```c
// This include is for the AWS IOT library that we installed
#include <AWS_IOT.h>
// This include is for Wifi functionality
#include <WiFi.h>

// Declare an instance of the AWS IOT library
AWS_IOT hornbill;

// Wifi credentials
char WIFI_SSID[]="";
char WIFI_PASSWORD[]="";

// Thing details
char HOST_ADDRESS[]="";
char CLIENT_ID[]= "Thingamajig";
char TOPIC_NAME[]= "$aws/things/Thingamajig/shadow/update";

// Connection status
int status = WL_IDLE_STATUS;
// Payload array to store thing shadow JSON document
char payload[512];
// Bit for sending the MQTT message
int sendMessageBit = 0;

// Pin for the button
const int INTERRUPT_PIN = 0;

// State for the button press
volatile byte state = LOW;
```

Copy over your Wifi and Thing settings from the previous Lab. As a reminder, the Wifi credentials are on these lines:

```c
char WIFI_SSID[]="";
char WIFI_PASSWORD[]="";
```

and the host address for your thing:

```c
char HOST_ADDRESS[]="";
```

Replace the **setup()** method with the following code:

```c
void setup() {
  WiFi.disconnect(true);
  Serial.begin(115200);
  // initialise AWS connection
  while (status != WL_CONNECTED) {
    Serial.print("Attempting to connect to Wifi network: ");
    Serial.println(WIFI_SSID);
    status = WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
    delay(5000);
  }
  Serial.println("Connected to the Wifi!");
  if(hornbill.connect(HOST_ADDRESS,CLIENT_ID)== 0) {
    Serial.println("Connected to AWS, bru");
    delay(1000);
  }
  else {
    Serial.println("AWS connection failed, Check the HOST Address");
    while(1);
  }
  // Set the button bin to a specific mode
  pinMode(INTERRUPT_PIN, INPUT_PULLUP);
  // Attach the method to call when the button is pressed
  attachInterrupt(digitalPinToInterrupt(INTERRUPT_PIN), buttonPress, CHANGE);
}
```

Next up we're gonna change the **loop()** method to update our thing shadow once the button has been pressed. Replace your **loop()** method with the following fragment:

```c
void loop() {
    if (sendMessageBit == 1) {
      sendMessage();
      sendMessageBit = 0;
    }
}
```

**=====Code Explanation Start=====**

You can see that we check to see if the **sendMessageBit** is set to 1 (true). 

```c
if (sendMessageBit == 1)
```

If true, we then call the **sendMessage()** method and reset the **sendMessageBit** to 0 (false).

```c
    sendMessage();
    sendMessageBit = 0;
```

**=====Code Explanation End=====**

Now we create a new method called **buttonPress()**. Paste the following fragment after the **loop()** method:

```c
void buttonPress() {
  state = !state;
  if (state == HIGH) {
    Serial.println("Button pressed");
    sendMessageBit = 1;
  }
  if (state == LOW) {
    Serial.println("Button released");
  }
}
```

**=====Code Explanation Start=====**

This method checks if the ESP32's button has been pressed.

```
    if (state == HIGH)
```

If so, we increment the counter and then set the **sendMessageBit** to 1 so our loop knows to update the thing shadow:

```
    counter++;
    sendMessageBit = 1;
```

**=====Code Explanation End=====**

Lastly, we create a method to update our thing shadow's button attribute. Paste the following fragment after the **buttonPress()** method:

```c
void sendMessage() {
  int temperature = random(0, 30);
  sprintf(payload,"{\"state\":{\"reported\":{\"temperature\":\" %d \"}}}",temperature);
  Serial.println(payload);
  if(hornbill.publish(TOPIC_NAME,payload) == 0) {
    Serial.println("Message was published successfully");
  }
  else {
    Serial.println("Message was not published");
  }
}
```

**=====Code Explanation Start=====**

The relevant lines of code are:

```c
int temperature = random(0, 30);
sprintf(payload,"{\"state\":{\"reported\":{\"temperature\":\" %d \"}}}",temperature);
```

You can see that we are setting the temperature to a random value between 0 and 30.

**=====Code Explanation End=====**

Compile and upload the Sketch to our ESP32 device.

Confirm that the application has connected to AWS by navigating to **"Tools -> Serial Monitor"** and examining the logs.

Navigate to **"Manage -> Things"** from the **AWS IoT** left navigation then click on the **"Thingamajig"** thing.

<p align="center"> 
<img src="images/thing_created.png" width="85%">
</p>

Click **Shadow** from the left navigation and scroll down to **"Shadow state:"**. Once you are ready, press the button on the device to initiate a temperature update to the device shadow. If the temperature reported is <=25 Celsius you will receive an Email otherwise you will receive an SMS (temperature > 25 Celsius)

<p align="center"> 
<img src="images/hightemp.png" width="35%">
</p>

The email will look similar to the following:

<p align="center"> 
<img src="images/email.png" width="85%">
</p>

And the SMS: 

<p align="center"> 
<img src="images/sms22.png" width="55%">
</p>

### What did we learn?

- How to create an SNS topic for SMS and email delivery
- How to create a subscriber for your SNS topic
- How to create a Lambda function
- How to create an IoT Rule and Action
- How to send an email/sms when your ESP32's on-board button is pressed and the "temperature" spikes above 25C

All labs completed! Phew. 

## Conclusion

Well I'm kinda impressed. You completed this workshop and most likely learned quite a bit about IoT. You got to play around with a device and might have even made some new friends. If not, we don't accept refunds :/ As a parting gift, check out the References section below and hit up the workshop author if you'd like to take the red pill. 

## References:

Intro to AWS IoT:
https://www.aws.training/learningobject/video?id=16505

IoT Building Blocks:
https://www.youtube.com/watch?v=HEQkVHxu46A

Thing/Device Shadow:
https://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-shadows.html

IAM Policies:
https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html

AWS IoT Rules:
https://docs.aws.amazon.com/iot/latest/developerguide/iot-rules.html

Arduino IDE: https://www.arduino.cc/en/Main/Software

Arduino Reference:
https://www.arduino.cc/reference/en/

Arduino Constants:
https://www.arduino.cc/reference/en/language/variables/constants/constants/

ESP32 Arduino core:
https://github.com/espressif/arduino-esp32

Hornbill:
https://github.com/ExploreEmbedded/Hornbill-Examples/tree/master/arduino-esp32/AWS_IOT

VSP Drivers:
https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers

NodeMCU on ESP32:
https://github.com/nodemcu/nodemcu-firmware/tree/dev-esp32

ESP32 Pin Mappings:
<p align="center"> <a href="https://github.com/espressif/arduino-esp32#esp32dev-board-pinmap">
<img src="images/ESP32-pins.jpg" width="75%"></a>
</p>

>**Note**: Pin mappings vary depending on the board that is used. More so, each one corresponds to a specific type and must be used with sensors that are compatible. In this case we are referencing an led that is soldered onto the board and which has an addressable pin. Similarly, if we for example, plugged in a sensor to pins 4 and 5 then we could address that sensor using those pins. As another example, pins like 8 and 9 can be used for TX/RX which is what's required to connect a temp/humidity sensor.
