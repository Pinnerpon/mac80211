# mac80211
http://lxr.free-electrons.com/source/net/mac80211

The data from Ethernet frames coming out of the Linux wireless driver needs to be intercepted before they are either sent between clients or into the network stack.

This is best done in [`rx.c`](http://lxr.free-electrons.com/source/net/mac80211/rx.c) of [mac80211](https://wireless.wiki.kernel.org/en/developers/documentation/mac80211) since [`ieee80211_deliver_skb()`](http://lxr.free-electrons.com/source/net/mac80211/rx.c#L2073) seems to be responsible for sending Ethernet frames between clients connected to the same wireless access point (WAP).

Ethernet frames need to be written to a log file in order to observe if and where they are being sent.

This is the format of `ethernet_frame_headers.txt`:

```
Frame [Number]: [Source MAC address]|[Destination MAC address]|[EtherType]|[Source IP address]|[Destination IP address]|[IP protocol]
```

Use `ieee80211_deliver_skb()` to control traffic by blocking it to a particular device, since it forwards packets between stations on the WAP.

On line [`2108`](http://lxr.free-electrons.com/source/net/mac80211/rx.c#L2108) of `rx.c`, the pointer to a received Ethernet frame is assigned to the transmit buffer:

```
xmit_skb = skb;
```

If only the pointer was assigned instead, and if the Ethernet frame contains a certain value (e.g. a specific MAC or IP address), which Ethernet frames are forwarded or dropped can be controlled.