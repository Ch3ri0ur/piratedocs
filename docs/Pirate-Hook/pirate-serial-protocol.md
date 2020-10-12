# Pirate Serial Protocol

The Pirate Serial Protocol is used by the [Pirate Hook](00-hook.md) and the [Pirate Bridge](../Pirate-Bridge/00-bridge.md) to transmit data between them. It was for the usage with an [Arduino](Theory/arduino.md) and a Raspberry Pi generated and uses the serial connection. The protocol makes the [Arduino](Theory/arduino.md) to a Master and the the Raspberry Pi to a Slave of the communication.

The Master side of this Communication is implemented by the [Pirate Hook](00-hook.md) and is limited by the Arduino performance. It needed to be less intensive in computing time and bandwidth. Also it shouldn't block the loop of the Arduino to much or other needed calculations and action on the single processed controller could be delayed. For this the Master side of the Protocol will use the data in its byte format directly from the Memory and will not need to perform any parsings. This also shrinks the amount of bytes to send in the Serial Communication and generates fixed size Messages. Also it will never need to wait on the Slave, it will send asynchron Data or request some Data, which also will arrive asynchron. The reason for the Request is to stop an overflow in the input buffer of the Master.

The Slave in this case will be the [Pirate Bridge](../Pirate-Bridge/00-bridge.md), that will have to read all incoming data and wait for delimiter and messsagetype symbols from the Master. That shouldn't be a problem for this side, because we expect that it would be a Raspberry Pi or something equal or better. It should own an OS to manage tasks, so that blocking couldn't occurs. With the multi core System and high Clock frequency ([Arduino](Theory/arduino.md) Uno 16MHz) the parsing and handeling of the data on event base shouldn't be a problem. With a virtual buffersize of 4KB an overflow shouldn't happen, too.

To allow different [Arduino](Theory/arduino.md) Boards as Master, even if the byte size of some datatypes are different, the Master sends at the start the byte size of each datatype and its own Buffersize. This way the parser in the Slave can modify its data the way it needs to be.

To make the Website [Pirate Flag](../Pirate-Flag/00-flag.md) as dynamic as possible and to reduce the effort of creating one, the Master also sends information about each generated Send and Receive variable. This way the website generates from this data directly a basic layout with all Components. The informations contain Name, Type and some more parameter that will be listed in a section below. 


## Master to Slave

All Messages from the Master to the Slave End with a Delimiter, so that the Slave can easy separate the different messages.
```
0xff, 'P', 'i', 'r', 'A', 't', 'E', '\n'
```
The length of the Delimiter is chosen to never get mixups with data. The Newline and the choice of readable simples was chosen to allow reading of the data Stream in Serial Terminal from the Arduino IDE.

<a id="Datatypes"></a>
Data containing messages get signed with a Datatype Symbol, these Symbols are listed in the table below.



|   Datatype    | Symbol |
| :-----------: | :----: |
|      int      |   I    |
| unsigned int  |   U    |
|     long      |   L    |
| unsigned long |   u    |
|     float     |   F    |
|    double     |   D    |
|     byte      |   B    |
|     word      |   W    |
|     bool      |   b    |
|     char      |   C    |
|    char[]     |   S    |

The Master to Slave Communication can be separated in 3 different Categories
- Initial Informations
- Sending of Informations
- Requesting of Informations



### Initial Communication

To allow the Website auto generation and inform the Slave what data can be Received or Send and what bytesize is used for the different messagetypes



byte PirAtE_SERIAL_START[PirAtE_SERIAL_START_LENGTH] = {0xee, 'P', 'i', 'r', 'A', 't', 'E', '\n'};

PirAtE_DATATYPE_INFO 'P'

PirAtE_TRANSMIT_SEPERATOR '$'


[Datatype Symbol](#Datatypes)
### Msg Types 
| Symbol |               Msgtype                |                       Style                       |                   Content                   |
| :----: | :----------------------------------: | :-----------------------------------------------: | :-----------------------------------------: |
|   P    |            Datatype Sizes            |                P[Type][Bytes]\$..                 | All datatypes with Size in Bytes get Listed |
|   T    |     Configuration of one SendMsg     | T[ID]\$[Name]\$[Type]\$[DefaultV]\$[MaxV]\$[MinV] |  Every Definition of a Send Msg gets Send   |
|   t    |   Configuration of one ReceiveMsg    |         t[ID]\$[Name]\$[Type]\$[DefaultV]         | Every Definition of a Receive Msg gets Send |
|   R    |         Request for new Data         |                         R                         |        Request to Node for more Data        |
|   I    |      Data Msg containing an int      |                I[ID][ValueInBytes]                |                 sizeof(int)                 |
|   U    | Data Msg containing an unsigned int  |                U[ID][ValueInBytes]                |            sizeof(unsigned int)             |
|   L    |     Data Msg containing an long      |                L[ID][ValueInBytes]                |                sizeof(long)                 |
|   u    | Data Msg containing an unsigned long |                u[ID][ValueInBytes]                |            sizeof(unsigned long)            |
|   F    |     Data Msg containing an float     |                F[ID][ValueInBytes]                |                sizeof(float)                |
|   D    |    Data Msg containing an double     |                D[ID][ValueInBytes]                |               sizeof(double)                |
|   B    |     Data Msg containing an byte      |                   B[ID][1Byte]                    |                sizeof(byte)                 |
|   W    |     Data Msg containing an word      |                   W[ID][2Bytes]                   |                sizeof(word)                 |
|   b    |     Data Msg containing an bool      |                   b[ID][1Byte]                    |                sizeof(bool)                 |
|   C    |     Data Msg containing an char      |                    C[ID][Char]                    |                sizeof(char)                 |
|   S    |    Data Msg containing an char[]     |                   S[ID][String]                   |                      *                      |

[*1]
```
hallo ich bin code
```


*MaxLength depends on buffer size and Overhead
PirAtE_RECEIVE_DATATYPE_STRING_MAXLENGTH = PirAtE_Serial_Buffer_Size - PirAtE_CHARARRAY_END_LENGTH - PirAtE_MSG_DATAID_LENGTH
PirAtE_MSG_DATATYPE_STRING_MAXLENGTH = PirAtE_MSG_DATA_MAXLENGTH - PirAtE_CHARARRAY_END_LENGTH


//Offset of actual Data to DataMsg Start
PirAtE_MSG_DATA_OVERHEAD (PirAtE_MSG_DATAID_LENGTH + PirAtE_MSG_DATATYPE_LENGTH)
//MaxData Length
PirAtE_MSG_DATA_MAXLENGTH (PirAtE_Serial_Buffer_Size - PirAtE_MSG_DATA_OVERHEAD - PirAtE_MSG_DELIMITER_LENGTH)


## Slave to Master


arduino hook master low computing time slave bridge event triggered

rewrite to hook and bridge ignore arduino in protocol just that master slow low computing time and cannot manage tasks and bridge faster more time can manage task event driven

simplyfy master sends type info msg info recv and send 
send happens asynchronus  and compacted

recive after request, request can be repeated immedatly except "no data" was reviced  else send new request after interval after last request to not spam events. data is send compacted in buffer size because request only when empty







## General Aspects

All symbols used by the protocol on the [Pirate Hook](00-hook.md) site are based on ASCII values (see http://www.asciitable.com/).
The message type symbols are often a repräsentation of the content. Means an Integer Message will be signed with an 'I'. This makes it easier to read the stream from the Arduino in the console and allows investigation and tests without the [Pirate Bridge](../Pirate-Bridge/00-bridge.md).
ID's of receive and send messages have an offset of 0x30 (48), so that the Index 0 is a  char '0' in ASCII. This way when observing a the arduino output and input the ID is a printable char character and Index 0-9 can be directly read.


## [Pirate Hook](00-hook.md) to [Pirate Bridge](../Pirate-Bridge/00-bridge.md)
All Messages from the Arduino have to Start with a Unique Symbol to show its content

All Messages from the Arduino have to End with a Delimiter so the Messages can be 

All Strings End with an '\0'

PirAtE_NO_DATA 0x29

//Value Offset 0x30
PirAtE_MSG_DATAID_OFFSET ((byte)'0')




## Node to Arduino

The Arduino Side is kept small, means the Node side hast to Convert the Data in bytes before sending

|  Symbol  |    Msgtype    |       Style        |                    Content                    |
| :------: | :-----------: | :----------------: | :-------------------------------------------: |
|   0x29   |    No Data    |        0x29        | Informs the Arduino that no Data is available |
| >=0x30 | Data Transfer | [ID][ValueInBytes] |              Transfer of a Value              |





