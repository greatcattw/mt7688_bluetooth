smart 7688 Blue tooth links to bt-uart module.
==========
img version : 0.94
kernel : 3.18.23
sw pkg :
	lks7688.img, offical image
	bt_uart_patch.tar, patch of bluez ipk
peripheral :
	smart_7688 module
	bluetooth USB dongle
	HC-06 blue tooth UART moudule
==========
power on HC-06.
plug in the bluetooth USB dongle.

flash smart_7688 module with offical image of lks7688.img
auto reboot

copy patch to usb disk
plug in the usb disk to smart_7688 module
install ipk package of bluez ipk
	cd /tmp/run/mounted/sda1/bt_uart_patch
	./gcat.23.sh
	sync
	reboot

==========
Try to that smart_7688 module connects to HC-06.
Basic way to check usb bt dongle 
	hciconfig
		hci0:   Type: BR/EDR  Bus: USB                                                  
	        BD Address: 00:10:60:E3:BC:3D  ACL MTU: 310:10  SCO MTU: 64:8           
	        DOWN                                                                    
	        RX bytes:494 acl:0 sco:0 events:21 errors:0                             
	        TX bytes:82 acl:0 sco:0 commands:20 errors:0

Try to link to HC-06 module. HC-06 is a bluetooth to UART module,
into bluetoothctl dialog
	bluetoothctl

	power on
		[CHG] Controller 00:15:83:0C:BF:EB Powered: yes

	agent on
		Agent registered

	scan on
		...
		#wait and until show below ...
		[CHG] Device 98:D3:31:B2:3B:EF Name: HC-06
		[CHG] Device 98:D3:31:B2:3B:EF Alias: HC-06

	scan off

check that WMU6204-Bluetooth had found HC-06
	devices
		...
		Device 98:D3:31:B2:3B:EF HC-06

link HC-06
	trust 98:D3:31:B2:3B:EF
		Changing 98:D3:31:B2:3B:EF trust succeeded

	pair 98:D3:31:B2:3B:EF
	#wait and until show below ...
	Request PIN code
	[agent] Enter PIN code:
	1234

exit bluetoothctl dialog
	exit

create bt-uart device node
	rfcomm bind 0 98:D3:31:B2:3B:EF

check device node
	ls -l /dev/rfcomm0
		crw-r--r--    1 root     root      216,   0 Sep 27 09:54 /dev/rfcomm0

test HC-06 bt-uart
	picocom -b 115200 /dev/rfcomm0

	# HC-06 LED turn on instead of blinking
	# Shorted HC-06 TX and RX
	# HC-06 echo what you type in.
	# exit picocom : [ctrl]+[a] + [ctrl]+[x]
