---
layout: posts
---
Using python sockets I have created a raw implemenation of Ping, Traceroute, Web server and a proxy server. These are all on the same python program and can be called using `python3 NetworkApplications.py <name of command> <base arguement if ping or traceroute> -<additional arguements>`.

**Ping**

The first functionailty that the program offers is Ping, the implementation that I made resembles the [Unix ping command](https://man7.org/linux/man-pages/man8/ping.8.html). My implementation doesnt offer as much options as the linux implementation does but it does allow the user to set the timeout (with `-t<time>`) and the amount of times that they want to ping an address (with `-c<amount>`). The user can either enter in the Ip address or the host name either one will work and to end the ping when a count is not set you have to press `CTRL + C`. Upon ending the program the user will see the minimum, average and maximum delay of all pings that were sent.

Here is an example of a ping to google and is done using the command `python3 NetworkApplications.py ping google.com`

![](https://michael-perdue.github.io/assets/GooglePing.PNG)


**Traceroute**

The second functionality that the program can offer is traceroute, once again the implemenation that I made resembles the [Unix traceroute command](https://man7.org/linux/man-pages/man8/traceroute.8.html). My implementation doesnt offer all of the unix options but it does let the user choose between using ICMP packets and using UDP packets (with `-p<protocol>` by default it uses ICMP) and set a timeout until packets are counted as lost (with `-t<time>`). It tries to send 3 packets to each hop and record the RTT of each packet, it will also note whether any packets are lost (if a packet is lost a * will be printed rather then the RTT) and what IPs the packets are sent to if they are sent to different IPs then each IP will be printed on a seperate line. Upon the finding of the destination or the max TTL is reached then the total packets sent is printed along with the amount of packets lost. You can also exit the program at any time with `CTRL + Z`. You can give an IP address or a hostname.

Here is an example of a udp traceroute to googles DNS and is done using the command `python3 NetworkApplications.py traceroute 8.8.8.8 -t1 -tpudp`
![](https://michael-perdue.github.io/assets/GoogleTraceroute.PNG)
