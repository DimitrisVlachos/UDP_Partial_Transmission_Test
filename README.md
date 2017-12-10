**UDP partial block transmission.**

**The protocol in detail :**

1. Client/Server communicate with a single fixed-size  packet
	  :
	  
HEADER:

	b0   : Command : 0 : Download request + Filename , 1 : Transmit requested file
	
command b0 : Request file

	b1..b2 : Filename length
	
	b2..filename length : filename to request (MAX 255 characters)

command b1 : TRANSMIT

	b1...9 : uint64_t : packet number (to be sorted later) (NOTE : 2^64-1 indicates FINAL BLOCK!)
	
	b9..17 : uint64_t : size of data associated with this packet
	
	b17..25 : uint64_t : crc32 of data
	
	b25.. size of packet : data...



2.Block size is fixed to 4KB (including header data).

3.Blocks are stored temporarily to a separate directory in client's download history directory

4.Once all blocks are transmitted , the client sorts blocks based on index and merges them to a single file.


**Improvements that can be applied :**

-Make management of incoming requests (assigned to same client) a separate process (Thread)
and handle net i/o seperately for each request. Terminate connection on completion or timeout.

-Allow variable-length block packets by expanding the header to support extra commands.
Example , on initial connection attempt , let the server decide which is the most optimal
block size for the requested file based on the following criteria :

	a.Limit Total server OUTGOING TRAFFIC load (If traffic load exceeds a certain amount , limit block sizes globally)
	
	b.In addition to [a] , pause outgoing packets temporarily
	
	c.Implement priority quee by implementing client accounting system.Registered clients with higher priority badge
	get more bandwidth


-Block compression
