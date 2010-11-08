!SLIDE center

# Websockets / Cramp

!SLIDE smbullets

## Websockets

* Persistent connections over HTTP. 
* Send and receive data fast. "TCP for web"
* Extension of HTTP, nice.
* Websocket API, which is implemented by the browsers
* Spec in draft
* http://www.whatwg.org/specs/web-socket-protocol/

!SLIDE smbullets

## Some browsers

* Chrome
* Safari > 4
* Firefox > 4
* No IE

!SLIDE smbullets
## How it works (briefly)
- Websocket connection is two parts
- A handshake and then data transfer

!SLIDE smbullets

## ws request
- Request defines 'Upgrade: Websocket' HTTP header
- 'Websocket-Sec-key*' headers sent so server can prove it's a websocket connection

!SLIDE

## Example ws request

    @@@ Internet
    GET /chat HTTP/1.1
    Upgrade: WebSocket
    Connection: Upgrade
    Host: localhost:4000
    Origin: http://localhost:8080
    Sec-WebSocket-Key1: 9!8     f0492 0  16
    Sec-WebSocket-Key2: -  1 50o46sh21Y*  A_ ,608


!SLIDE smbullets
## ws response
- Response is then a protocol handshake
- With challenge response
- Two numbers computed from Key1 and Key2, the number divided by the spaces

!SLIDE

## Example ws response

    @@@ Internet
    HTTP/1.1 101 Web Socket Protocol Handshake
    Upgrade: WebSocket
    Connection: Upgrade
    Sec-WebSocket-Origin: http://localhost:8080
    Sec-WebSocket-Location: ws://localhost:4000/chat

    \ CHALLENGE response as big-endian 32-bit number's \

!SLIDE smbullets

## Connection ready for data

- Now data transfer can take place
- Data transferred with defined encapsulation packets
- For more info - read the spec
- http://www.whatwg.org/specs/web-socket-protocol/

