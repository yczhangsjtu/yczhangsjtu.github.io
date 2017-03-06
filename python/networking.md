---
title: Python Networking
---


# Python Networking

## Socket

The **Berkeley Software Distribution**(BSD) socket is the primary means for communication between computers. It is the interface between network applications and network stack.  

In python, to use socket, simply import the socket library by

```python
from socket import *
```

To create a socket, use the constructor with some arguments.

```python
sock = socket(AF_INET, SOCK_STREAM)
```

Here `AF_INET` specifies the address family IPv4. If `AF_INET6` is used, then the address family would be IPv6.  

The `SOCK_STREAM` specifies that this is a TCP socket. As we know, communicating with TCP is like accessing a file stream. If you want to create a UDP socket, use `SOCK_DGRAM` instead.

## Bind

After creating the socket, the next step is to bind it with a specific port on some host. The way to do this in python is quite straightforward.

```python
sock.bind((host,port))
```

Note that there is only one argument which is a tuple of two elements. If you by mistake pass the two arguments separately, an error would occur.  

Here, `host` is a string while `port` is an integer. If the host is an empty string, the socket would be bound to the localhost. That is, code like

```python
sock.bind(('',5000))
```

will bind the socket to port 5000 on localhost.

## Listen

If your socket is bound to a localhost port, the next thing it has to do is listening. The only parameter needed by this method is the maximal number of connections this socket can maintain. Usually this is set to one.

```python
sock.listen(1)
```

So the socket is put into listening mode.

## Accept

The socket now is listening to the port, but not really ready to connect with a remote host. The `accept` method will put the program into busy waiting mode. That is, the method will not return until someone connects to this socket. And if someone does connect to it, the socket used by him and the address of the remote host will be returned. The `accept` method takes no arguments, but has two return values.

```python
conn, addr = sock.accept()
```

Here `conn` is a socket, and `addr` is a tuple of IP and port number, or possibly other informations.  

Now if you type these into the python command line and press enter, the program will just hang there. Open another terminal and type

```python
telnet localhost 5000
```

which permits the `telnet` client to connet to port 5000, which the socket is listening to, the accept method will return.  

Or, you can create another client socket, also by python, to connet to your listening socket.

## Connect

The creation of the another socket is just the same. A client socket does not need the bind process. It just connects.  

Open another terminal and open python.

```python
from socket import *
sock = socket(AF_INET, SOCK_STREAM)
sock.connect(('127.0.0.1', 5000))
```

Then you will find in the original terminal that the `accept` function returns. So the connection is successfully created.  

Note that the `conn` socket is created automatically by the system used for nothing other than communicating with the current remote client.

## Send & Receive

Now both the `conn` socket and the `sock` socket in the client side can use the `send` and `recv` methods to communicate with each other. Note that the `sock` socket of the server side is of no use in this process. Don't use that socket by mistake.

```python
sock.send("Message")
conn.recv(1000)
```

The `send` method takes a string as argument, which is exactly the message sent to the other side. While the `recv` method takes an integer as argument, which is the number of bytes the receiver hopes to receive, and returns a string. The string may not be the same length as the argument specifies. It can be shorter if the sender did not send that much information. But it will not be greater than the specified number.  

Now you may wonder, how is the communication synchronized? Well, it is not synchronized. The truth is, each side of the communication holds a buffer, and the `send` method of the other side simply put the information into the buffer. But if the receiver never calls the `recv` method, the information will just stay in the buffer.  

Now, clear? The process is just like communicating with mails. Each side can write to the other at any time, and the letters will be put into the mailbox of the receiver. But it is the receiver's business to check the mailbox, which is exactly what the `recv` method does. If the sender sends two strings in between of which the receiver did not call the receive method, then the next time the receiver calls the method, he will get a single string which is the concatenation of the two strings. This means the bytes from the sender just pile in the buffer of the receiver.  

Here are some problems.

*   What if the buffer is empty when `recv` method is called?  

    Well, the program will just hang there until the sender sends some message, even a single character would cause the `recv` method to return. Though it is not likely to happen in real life that when the receiver checked the mailbox and found nothing in it, he had to stay beside the box and wait, cannot even writing to tell the other side to hurry. This is why usually a real world communication application actually maintains two threads each containing a socket, one doing nothing but receiving message, another just sending messages.  

    Note that if there is one byte in the buffer, and the receiver calls `recv` with argument 2, the program will not hang there waiting for the other bytes, but just return a string that is not as long as expected. But if the receiver calls the receive method twice with argument 1, he would have to hang there and wait for another byte.
*   Is there any limitation on the size of the buffer?  

    The answer is yes. You can try to send `"Message"*1000000` to the other. Then you will find the program hanging there, waiting for the buffer to be emptied. Well, it does not have to be emptied, just needs to have enough room for the message. And once the long message is fully transmitted into the buffer, the method will return.

## Close

Do not forget to close the sockets by invoking the `close` methods. This will free the resources occupied by the sockets. For the listening socket, if you forget the close it, the port it is bound with cannot be used again.
