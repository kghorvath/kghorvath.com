---
title: "Trying Modem Emulation in 86Box"
date: 2024-08-20T17:03:41-04:00
draft: false
---

With the release of version 4.2, 86Box, the vintage x86 PC emulator, now has support for serial modem emulation. It supports acting as a Telnet client or dial-in server, but can also emulate a connection to the internet.

To configure it, there's a new adapter option in the Network settings tab of 86Box for a Standard Hayes-compliant Modem. From the settings, you'll be able to choose a free COM port as well as the baud rate. You can also point it to a phonebook file (more on this later). For this example, I'm using COM1 and 9600. You'll also need to go to the Ports (COM & LPT) tab and make sure your selected serial port is enabled, and that it is not being passed through.

![86Box Network Settings](/images/86box-modem-network.png)

Now you'll be able to add it within your 86Box VM. In my case, I'm emulating Windows 95 OSR2. From Control Panel > Modems > Modem Properties, add a new modem. Be sure to select "Don't detect my modem". On the next page, select the model as a "Standard 9600 bps Modem", replacing the baud rate with whatever you configured for 86Box.

![Windows 95 Install New Modem](/images/86box-modem-install.png)

You'll also need to select whichever COM port you assigned it in your configuration as well.

Once installed, you can add a new Dial-Up connection from the Accessories folder in the Start Menu. In Windows 98 and later, you can do this directly, but for Windows 95, you'll have to install SLIP and Dial-Up Scripting support first. You can do this through the Windows Setup tab of Add/Remove Programs. Select "Have Disk", and then browse to the Admin/Apptools/Dscript folder on your Windows 95 CD-ROM and select "Rnaplus.inf", and install the "SLIP and Scripting for Dial-Up Networking" option.

When adding a new dial-up connection, you can either leave the telephone number blank and enter 0.0.0.0 later as your connection number, or for more realism, you can point 86Box to a phonebook file on your host with a map of telephone numbers to hosts. In this case, if I want to use the number 404-555-1234 as my dial-in number to connect to the internet, I would add this to my phonebook.txt file:

`404-555-1234 0.0.0.0:0`

After creating the new connection, go into its properties and under the "Server Types" tab, change the Type of Dial-Up Server from PPP to SLIP. Under "TCP/IP settings", make sure all addresses and gateways are assigned by the server.

![Dial-Up Connection Properties](/images/86box-modem-properties.png)

Finally, you can go ahead and test your connection. Leave the username and password blank, and either put in your phonebook defined number, or 0.0.0.0 as the Phone number.

![Connect To](/images/86box-modem-connect.png)

If all is successful, you'll be greeted with a blank terminal screen dialog. Hit continue and you'll be connected!

![Connected!](/images/86box-modem-connected.png)
