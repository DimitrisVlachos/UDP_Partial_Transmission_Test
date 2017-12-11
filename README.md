**UDP partial block transmission.**

**The protocol in detail :**

1. Client/Server communicate with a single fixed-size  packet
	  :
	  
HEADER:

	b0   : Command 
	
command 0x00 : Request file

	b1..b2 : Filename length (255 max)
	
	b2..filename length : filename to request (MAX 255 characters / No Utf-16/32 support for this demo)

command 0x01 : TRANSMIT

	b1...9 : uint64_t : packet number (to be sorted later) (NOTE : 2^64-1 indicates FINAL BLOCK!)
	
	b9..17 : uint64_t : size of data associated with this packet
	
	b17..21 : uint32_t : crc32 of data
	
	b21.. size of packet : data...

command 0x02 : PACKET NOT RECEIVED (client)

	b1...9 : uint64_t : packet number (to be sorted later) (NOTE : 2^64-1 indicates FINAL BLOCK!)

command 0x03 : PACKET RECEIVED (client)

	b1...9 : uint64_t : packet number (to be sorted later) (NOTE : 2^64-1 indicates FINAL BLOCK!)
	
2.Block size is fixed to 4KB (including header data). (Can be changed at compile time)

	2.a : Client -> Server responses are fixed to 255+1+1 bytes
	
3.Blocks are stored temporarily to a separate directory in client's download history directory

4.Once all blocks are transmitted , the client sorts blocks based on index and merges them to a single file.


**Improvements that can be applied :**

-Make management of incoming requests (assigned to same client) a separate process (Thread)
and handle net i/o seperately for each request. Terminate connection on completion or timeout.

-Allow variable-length block packets by expanding the header to support extra commands.
For example , on initial connection attempt , let the server decide which is the most optimal
block size for the requested file.

-Network usage optimization:

	a.Limit Total server OUTGOING TRAFFIC load (If traffic load exceeds a certain amount , limit block sizes globally)
	
	b.In addition to [a] , pause outgoing packets temporarily
	
	c.Implement priority queue by implementing client accounting system.Registered clients with higher priority badge
	get more bandwidth


-Block compression

-Block encryption

-Utf-16/32 filename support
