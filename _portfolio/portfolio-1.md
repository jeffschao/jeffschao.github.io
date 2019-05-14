---
title: "E-Key"
excerpt: "Bluetooth remote key mechanism for Android<br/><img src='/images/ekeyarrow.png'>"
collection: portfolio
---

```python
def setupDataListener():
	global server_sock
	server_sock = BluetoothSocket( RFCOMM )
	server_sock.bind(("",PORT_ANY))
	server_sock.listen(1)

	port = server_sock.getsockname()[1]
	
	# advertise normally if no BLE
	if(not BLE):
		printF ("Starting non-BLE beacon")
		advertise_service( server_sock, "EKey Lock",
                   service_id = UUID,
                   service_classes = [ UUID, SERIAL_PORT_CLASS ],
                   profiles = [ SERIAL_PORT_PROFILE ], 
#                   protocols = [ OBEX_UUID ] 
                    )
	
	printF("Waiting for connection on RFCOMM channel %d" % port)
```

We wire a servomotor to a custom servo-mount, which is then wired to a raspberry pi.  Using Python Flask for iOS and Java for Android, we lock or unlock a key using the servo.