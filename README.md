# binary-attached
A package format mashup based on protocolbuffer and JSON, allows you to have JSON's dinamicity and felxibility with the ability to wrap binary data also without to need to convert it into space consuming base64 format.

The format overhead is between JSON and protocolbuffer. But its much more flexible than protocolbuffer. 

Basically **binary-attached** is a protocol buffuer scheme which has 

* an optional string field containing a JSON formatted data and 

* has zero or more binary fields containing your binary data.

This format doesn't specify the way how you refer your binary data chunks from the JSON part. It's up to you how you use it. If you need a connection between binary and non-binary data the easiest way to have an array in your JSON with the connected metadata elements for every binary chunk. Protocol buffer keeps the order of binary data chunks, so you can refer them easily.

Let's have a look at the scheme itself:

``` protobuf
message BinaryAttached {
	optional string meta = 1;
	repeated bytes  data = 2;
}
```
As you can see it is a very slim scheme, you can easily use it without any complicated protocolbuffer library. You just need to implement a specific encoder/decoder.

You can find a cool description of protocolbuffer's binary format here: https://developers.google.com/protocol-buffers/docs/encoding

### Binary
The first byte of every field describes it's type and number. In our case the meta part (which contains the JSON) has the 0x0A byte prefix. On the next 1-4 bytes we should give the length of our meta field. See **Base 128 Varints** section in the protocol-buffer documentation. After the length we write the actual JSON formatted string in UTF8.
For the binary data we use the 0x12 byte prefix. We also need to give the length of the binary chunk. We can repeate this field as many times as we need it.

#### Example
```
0A 18 7B 22 68 65 6C 6C 6F 22 3A 20 22 77 6F 72 6C 64 22 7D
12 15 62 69 6E 61 72 79 2D 61 74 74 61 63 68 65 64
```
The meta part contains the JSON string: `{"hello": "world"}`.
And the data part contains the binary chunk: `binary-attached`.

##License
[Beerware](http://en.wikipedia.org/wiki/Beerware)
