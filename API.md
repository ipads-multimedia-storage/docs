# API DOC
This document clarifies some important APIs in the project.

## Server to Client
The server first sends the length of json string to client(16byte, left-aligned), then sends json string.

The data structure of json is as following.
```json
{
  "bandwidth": <Double>, // the bandwidth of network  
  "object": { // one object each time
    	"id": <Long>, // id of the object
    	"time": <Long>, // time related to the location
    	"speed": <Double>, // the moving speed
    	"x": <Double>, // location x
    	"y": <Double>, // location y
    	"angle": <Double>, // angle of the object
	},
}
```

decode in client by:

```python
speed = response["speed"]
objects = response["objects"]
if (len(objects) > 0):
    for obj in objects:
        print("object (ID:{})".format(str(obj["id"])))
        print("\ttime: {}".format(str(obj["time"])));
        print("\tlocation: ({}, {})".format(str(obj["x"]), str(obj["y"])));
```

## Client to Server

The client first sends the length of json string to server(16byte, left-aligned), then sends json string, finally sends image bytes.(The reason of sending image bytes outside json instead of inside json is that json can't handle byte type very well, thus if we use json to transform bytes, we must encode bytes to string(using base64 etc.), which means significantly increasing the size of data)

The data structure of json is as following.
```json
{
  "length": <Integer>, // the length of image bytes
  "event_time": <Double>, // the time when camera captures the image  
  "current_time": <Long>, // the time when client sends this message(for bandwidth calculation only)
}
```