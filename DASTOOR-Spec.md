# PIRAN/DASTOOR Specification

* Name: PIRAN/DASTOOR
* Version: 1
* Status: draft
* Editor: Isa Hekmatizadeh esa.hekmat@gmail.com

DASTOOR specification define how dastoor application communicate with other nodes(safir and 
darbaan nodes) to monitor and manage them. dastoor application could be any central 
administration application which suppose to monitor and manage nodes which implements 
PIRAN/SAFIR-DARBAAN specification.

## License
Copyright (c) 2018 Isa Hekmatizadeh.

This Specification is free software; you can redistribute it and/or modify it under the terms of 
the GNU General Public License as published by the Free Software Foundation; either version 3 of 
the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; 
without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.  

You should have received a copy of the GNU General Public License along with this program; if 
not, see <http://www.gnu.org/licenses>.

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", 
"RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 
2119 (see "[Key words for use in RFCs to Indicate Requirement Levels](http://tools.ietf.org/html/rfc2119)").

## Goals
DASTOOR specification defines a protocol for communication between dastoor application as a 
central administration web application with SAFIR and DARBAAN nodes which act as channels and 
servers. This protocol define how each node SHOULD send its health information to dastoor 
application and how dastoor application send management commands to each node. By the help of 
this specification dastoor application can monitor and manage nodes across the cluster.

## Architecture
Dastoor application MUST bind a router socket and each node SHOULD connect its dealer sockets to 
it. Other nodes MAY discover dastoor on the network by the use of PIRAN/RBND protocol and with 
the role of 'administrator'. SAFIR nodes SHOULD send its service catalog to dastoor and after it 
they should repeatedly send health information. dastoor MAY send ADD/REMOVE command to the SAFIR 
nodes to update their service catalog. DARBAAN nodes also SHOULD send their health information. 

## Message Header
Every message transferred between Dastoor and other nodes SHOULD have a specific header. Other 
than frames used for routing in router/dealer messages, first frame MUST be "DST" followed by one
character specify the protocol version which in this version is "1" so first frame is "DST1". 
Second frame indicate the command type.

Command type of different messages are:
- "HLT" for health message
- "INTR" to send service catalog
- "RINTR" for request for service catalog
- "REMOVE" to remove service
- "ADD" to add or update service

## Health Message
Health message is a message from nodes to dastoor to indicate they are alive. Command type frame 
of this type of message is "HLT" and has just one frame indicate the node type.

Health message consist of:
* frame 0: "DST1" #indicate the protocol name and version
* frame 1: "HLT"
* frame 2: "SERVER"  or "CHANNEL" #node introduce its role

## Service Catalog Messages
After each server node connected to the dastoor they SHOULD send INTR message and if dastoor 
restarted unexpectedly it COULD send RINTR and each node MUST respond RINTR with an INTR message.
Format of INTR and RINTR messages is similar to corresponding command in PIRAN/SAFIR-DARBAAN 
Specification.

INTR message consist of:
* frame 0: "DST1" #indicate the protocol name and version 
* frame 2: "INTR" #indicate the command type
* frame 3: "example-service" #name of the service
* frame 4: "1.2" #version of the example-service
* frame 5: "another-service"
* frame 6: "1"
... 

RINTR message consist of:
* frame 0: Identity of the node socket
* frame 1: Empty frame
* frame 2: "DST1" #indicate the protocol name and version
* frame 3: "RINTR"

## Remove Command
Dastoor MAY send REMOVE command to any node, notify node to remove specific service, each node 
SHOULD un-deploy the service specified in REMOVE command.

REMOVE message consist of:
* frame 0: Identity of the node socket
* frame 1: Empty frame
* frame 2: "DST1" #indicate the protocol name and version
* frame 3: "REMOVE"
* frame 4: "some-service"  #service name
* frame 5: "1.2" # service version

## Add Command
Dastoor MAY send ADD command to any node, notify node to add or update a specific service, each 
node SHOULD add if it does not exist or update existing service specified in ADD command. The 
binary file of service (jar file) be fetched by the nodes in separate channel for file 
transferring between modules.

ADD message consist of:
* frame 0: Identity of the node socket
* frame 1: Empty frame
* frame 2: "DST1" #indicate the protocol name and version
* frame 3: "ADD"
* frame 4: "some-service" #service name
* frame 5: "1.2" # service version

## File Transfer
ADD command should be send with consequence file transferring. Jar file of the new service should
be transferred from dastoor to Safir node. File Transferring SHOULD be started by CHECK command 
from Safir, and Server MUST reply the CHECK command with requested file size and its SHA1 hash. 
Safir request each file chunk with separate FETCH command and Dastoor SHOULD reply each FETCH 
command with requested file chunk.

### CHECK Command
Safir node by CHECK command COULD request size and SHA1 hash of a specific service file. Server 
SHOULD reply CHECK command with FILE-INFO command.

CHECK command consist of:
* frame 0: "DST1" #indicate the protocol name and version
* frame 1: "CHECK"
* frame 2: "some-service" #service name of the file requested
* frame 3: "1.2" #service version of the file requested

### FILE-INFO Command
Dastoor SHOULD reply every CHECK command with corresponding FILE-INFO command. FILE-INFO message 
consist of the file size(in byte) and SHA1 hash of the file.

FILE-INFO command consist of:
* frame 0: Identity of the node socket
* frame 1: Empty frame
* frame 2: "DST1" #indicate the protocol name and version
* frame 3: "FILE-INFO"
* frame 4: "some-service" #service name of the file
* frame 5: "1.2" #service version of the file
* frame 6: 40000000 #indicate the Jar file of the requested service is 40Mb
* frame 7: "fb4f68864284b03e36c0f7b42f4e120202bb0a1b" #SHA1 hash of file

### FETCH Command
Safir nodes MAY fetch each chunk of a file by FETCH command. every FETCH command SHOULD be 
replied by a FILE-CHUNK message.

FETCH command consist of:
* frame 0: "DST1" #indicate the protocol name and version
* frame 1: "FETCH"
* frame 2: "some-service" # file's service name
* frame 3: "1.2" # file's service version
* frame 4: 1000001 # offset which chunk should start from
* frame 5: 250000 # size of the chunk in byte

above command request a chunk of service "some-service" version "1.2", 250000 bytes starting from
1000001th byte. 

### FILE-CHUNK Message
Dastoor SHOULD reply every FETCH command with a FILE-CHUNK message. There is a status frame in 
FILE-CHUNK message indicate the FETCH request is correct or not.

FILE-CHUNK command consist of:
* frame 0: Identity of the node socket
* frame 1: Empty frame
* frame 2: "DST1" #indicate the protocol name and version
* frame 3: "FILE-CHUNK"
* frame 4: "OK" #state frame which should be OK if everything is fine or contain error message if requested chunk is invalid.
* frame 5: "some-service" # file's service name
* frame 6: "1.2" # file's service version
* frame 7: 1000001 # offset which chunk start from
* frame 8: 250000 # size of the chunk in byte
* frame 9: [ bytes... ] #actual bytes of the chunk
