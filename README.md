# Enabling_DCTCP
How to on how to enable Datacenter TCP on Windows and Linux servers for a low latency network.

---

<b>INTRODUCTION</b>

Datacenter TCP (DCTCP) is a TCP congestion control algorithm developed to keep data network latency low even under load

Its application removes, or at the very least highly reduced the possibility of, bufferbloat on the network switches

Its best used on LAN, or in a network where expected latency does not exceed 10ms

For more information, its best to consult the references section, or call Dr Google.

---

<b>ENABLING ON WINDOWS SERVERS</b>

<b>Only works on Windows Server version 2012 and later</b>

To enable DCTCP on windows servers, open Powershell on the target server (either remotely or local, or use [LazyWinAdmin](https://lazywinadmin.com/p/scripts.html#LazyWinAdmin) for convenience) and run the following commands:

To check if there is a NetworkFilter applied, if any

<b>Get-NetTransportFilter</b>

To enable DCTCP you should specify your LAN and any other network as long as the latency does not exceed 10 milliseconds, so for example it would not be advisable to enable it to an IPSEC Site to Site VPN if the latency to the other end exceeds 10ms

In this example the LAN is 192.168.1.0/24

<b>New-NetTransportFilter -SettingName Datacenter -DestinationPrefix 192.168.1.0/24</b>

Check if the new TransportFilter is applied:

<b>Get-NetTransportFilter</b>

You will see your new filter enabled along with the default "INTERNET" one, and that's it.

---

<b>ENABLING DCTCP ON LINUX SERVERS</b>

On Linux it is far easier to enable DCTCP, you just need to edit the file /etc/sysctl.conf and add this line at the end

<b>net.ipv4.tcp_congestion_control=dctcp</b>

Save, and apply the changes with: <b>sudo sysctl -p</b>

---

<b>SEE ALSO: BBR CONGESTION CONTROL</b>

BBR is another network congestion control algorithm developed by Google to achieve high throughput, low latency, no bufferbloat

Google claims the adoption of the algorithm helped increase YouTube network throughput by 4% worldwide, and more than 14% in some countries

It is, at the time of this writing, only available on Linux (Kernel version 4.9 or newer)

To enable on Linux, edit the file /etc/sysctl.conf and add those two lines at the end of the file

    net.core.default_qdisc=fq</br>
    net.ipv4.tcp_congestion_control=bbr</br>

<b>remove DCTCP from the file if you have enabled it, there can be only one congestion control working</b>

Apply the changes with <b>sudo sysctl -p</b>

---

<b>CONCLUSION</b>

The adoption of DCTCP (Or BBR) will help your servers keep being able to handle connections with low latency even under stress, without changing infrastructure, while keeping a smaller load on the network switches

You can check and test network conditions by using Iperf3 tool (iperf.fr), there are binaries for Windows and Linux, and Android Applications.

---

<b>REFERENCES</b>

    DCTCP
    https://web.stanford.edu/~balaji/papers/10datacenter.pdf
    https://www.microsoft.com/en-us/research/publication/data-center-tcp-dctcp/

    BBR
    https://github.com/google/bbr
    https://research.google/pubs/pub45646/
