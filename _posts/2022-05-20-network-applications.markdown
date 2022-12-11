---
title: Network Applications
date: 2022-02-15 
categories: [University Projects, 2nd year]
tags: [python]     # TAG names should always be lowercase
---
Using python sockets I have created a raw implemenation of Ping, Traceroute, Web server and a proxy server. These are all on the same python program and can be called using `python3 NetworkApplications.py <name of command> <base arguement if ping or traceroute> -<additional arguements>`.

## Ping

The first functionailty that the program offers is Ping, the implementation that I made resembles the [Unix ping command](https://man7.org/linux/man-pages/man8/ping.8.html). My implementation doesnt offer as much options as the linux implementation does but it does allow the user to set the timeout (with `-t<time>`) and the amount of times that they want to ping an address (with `-c<amount>`). The user can either enter in the Ip address or the host name either one will work and to end the ping when a count is not set you have to press `CTRL + C`. Upon ending the program the user will see the minimum, average and maximum delay of all pings that were sent.

Here is an example of a ping to google and is done using the command `python3 NetworkApplications.py ping google.com`

![](https://michael-perdue.github.io/assets/GooglePing.png)


## Traceroute

The second functionality that the program can offer is traceroute, once again the implemenation that I made resembles the [Unix traceroute command](https://man7.org/linux/man-pages/man8/traceroute.8.html). My implementation doesnt offer all of the unix options but it does let the user choose between using ICMP packets and using UDP packets (with `-p<protocol>` by default it uses ICMP) and set a timeout until packets are counted as lost (with `-t<time>`). It tries to send 3 packets to each hop and record the RTT of each packet, it will also note whether any packets are lost (if a packet is lost a * will be printed rather then the RTT) and what IPs the packets are sent to if they are sent to different IPs then each IP will be printed on a seperate line. Upon the finding of the destination or the max TTL is reached then the total packets sent is printed along with the amount of packets lost. You can also exit the program at any time with `CTRL + Z`. You can give an IP address or a hostname.

Here is an example of a udp traceroute to googles DNS and is done using the command `python3 NetworkApplications.py traceroute 8.8.8.8 -t1 -tpudp`

![](https://michael-perdue.github.io/assets/GoogleTraceroute.png)


## Web Server

The third functionality that the program can offer is a Web server that hosts the html files that are put in the same folder as the code. It uses port 8080 so to connect to the website you use 127.0.0.1:8080/index.html, upon a valid HTTP GET request it will return the file back the client with the correct header that has a 200 status code. If the file does not exist then it will return back a 404 not found error. You can also configure what port it is on with `-p<port number>`.

To run the program you enter the following command `python3 NetworkApplications.py web -p8080`

## Proxy Server

The fourth functionality that the program can offer is a web proxy server that can handle HTTP/1.1 GET requests. It works through the user sending a request to the server through a socket and then the server will try to get the address that the user wants to connect to and also checks if there is a specific port that the user wants to connect to. If it cannot connect within 5 seconds it will send back an error message otherwise it will forward on the users request to the web server they want to connect to and will send back all the messages that it recieves from the web server back to the client. By default the proxy server will be launched on the port 8000 but you can also configure what port it is on with `-p<port number>`.

To run the program you enter the following command `python3 NetworkApplications.py proxy -p8000`
