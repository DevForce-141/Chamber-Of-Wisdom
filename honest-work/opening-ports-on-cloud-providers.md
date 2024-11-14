### Source: Honest Work (Written by Myself)

## Overview
Cloud Providers Generally have a firewall at account level before OS firewall of your VM.
You would need to open ports in both places. It's OBSERVED that there's a short TTL of few seconds
upto 1 minute for port changes to take effect. So give it a bit if the result is unchanged


### Microsoft Azure
Azure has a firewall at Portal account level as well. First, you need to open ports
there before attempting to open/allow in VM firewall. You need to be careful while
opening ports as source & destination ports matter. The destination port is the port
you're trying to open. The source port is the port which will be used to send request to VM.
Generally, you should leave it "*" or it's equivalent of "any" as you will not have control
over it. However, if you know the port, then you should define it.


#### Troubleshooting Tips
If ports are not opening, start with full access and gradually tighten down.
1. Make sure you're listening on the port before trying to test it.
``[Insert cmd command that shows the listener if a listener is working]``
2. First step should be to open all source & destination ports.
Then you should switch to allow all source ports but specific destination port or vice versa.
