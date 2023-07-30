# Grass Valley Director

## 2023-07-29

I was able to get a few Grass Valley Director control panels for a reasonable price, due to them being damaged.

It appears that the T-Bar is very susceptable to damage. The outer plastic housing also is easily broken.

![image](https://github.com/dslik/kb-notes/assets/5757591/1bab4e49-2e1a-4f99-ae5a-b33c8ba3ac71)

Let's take a look inside:

![image](https://github.com/dslik/kb-notes/assets/5757591/0d7c5972-0046-4fdc-a83d-c3f0b3d50cd0)

Looks like a pretty stock SBC, with dual DisplayPort, USB3, Ethernet and an integrated touchscreen controller. Looks like GV designed the main board, though I suspect that the CPU is COTS, specifically PICMG COMexpress.

The red/black cables at the top middle of the SBC connect to the main LCD. The Black/Red/Green/White cables further to the right are USB cables.

The power supply is also pretty normal looking, though there are some non-standard connections going to the SBC. At least one of those is the soft power on/off control.

![image](https://github.com/dslik/kb-notes/assets/5757591/e4d33447-72b9-442d-911a-5ad9a3b4d178)

Here's how the T-Bar was broken — The mounting flanges on the top and bottom have shattered off. No easy fixes other than trying to use some epoxy to fix them.

![image](https://github.com/dslik/kb-notes/assets/5757591/20806c10-cda7-40a0-aca8-43bda6ebbca2)

If I were to re-purpose this in another piece of equipment, I could use the four mounting holes on the left side.

The PCB on the T-Bar is a GV part, as is the mechanical assembly. The PCB on the right in the above photo is a eGalax_eMPIA USB touch screen controller.

Taking out the T-Bar, it appears to be completely mechanical, rotating a magnet placed right beside an IC on the PCB.

![image](https://github.com/dslik/kb-notes/assets/5757591/0e16371b-e03d-4444-a4f6-7a0e7ba64293)

The IC is an AMS [AS5045](https://ams.com/en/as5045) - a 12-bit Rotary Position Sensor, All of the pins from the IC are connected to a ribbon cable, which connects to a controller board (specifically, to U1, an 8051).

![image](https://github.com/dslik/kb-notes/assets/5757591/db048336-46f2-4067-b512-f763ac414493)

This board connects to the SBC via USB, and includes a NEC μPD720114 USB hub, with three cables going out to each button/display board.

That it uses USB makes these boards a lot easier to re-use, since I can hook it up to a USB analyzer and write my own driver.

There are three "User Interface" boards, each with eight buttons and an OLED.

![image](https://github.com/dslik/kb-notes/assets/5757591/21c9e5f4-87dc-4699-a7b9-4948ab583728)

They connect via USB and are powered via USB. And yes, each board has another 8051.

Here's what the front of the board looks like:

![image](https://github.com/dslik/kb-notes/assets/5757591/9b247990-e3a1-4eaa-bd1b-af18eb5d31b7)

All in all, lots of good possibilities for something almost completely bespoke.

Next steps will be to get or make some little USB adapters so I can insert my USB analyzer between the USB hub and the User Interface board, and see what it looks like.

When the system is started, it boots into Windows:

![image](https://github.com/dslik/kb-notes/assets/5757591/a19e66e6-06f5-4e1d-bb94-1f77a3857973)

The SBC is a Core i5-3610ME at 2.7 GHz, with 7 GB of RAM.

![image](https://github.com/dslik/kb-notes/assets/5757591/7fdf7001-d0a0-45dd-8bea-87907bafe693)

There is a panel diagnostic utility installed , which lets you control button colours and the displays. This will make figuring out the USB protocol a lot easier.

![image](https://github.com/dslik/kb-notes/assets/5757591/a507022b-961a-40d6-b0ad-505129f8335d)
