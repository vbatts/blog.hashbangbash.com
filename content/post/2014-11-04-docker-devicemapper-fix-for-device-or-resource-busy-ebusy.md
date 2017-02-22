---
title: 'docker: devicemapper fix for `device or resource busy` (EBUSY)'
author: vbatts
layout: post
date: 2014-11-04T23:17:16+00:00
url: /2014/11/docker-devicemapper-fix-for-device-or-resource-busy-ebusy/
categories:
  - code
  - Linux
  - Projects
tags:
  - devicemapper
  - docker
  - Linux
  - namespaces

---
**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
``````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
`````` 
  
This is the unshared PID we started earlier.

Notice also, we can determine for sure they are on different mount namespaces by checking the following:
  
```````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
``````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
`````` 
  
This is the unshared PID we started earlier.

Notice also, we can determine for sure they are on different mount namespaces by checking the following:
  
``````` 
  
Notice they are referencing different numbers here.

Now to cleanup, we can stop/kill this unshared PID, and \``docker rm ...`\` our test container.

**Solution**:
  
The solution to this issue is to have the docker docker itself run in an unshared mount namespace. Unfortunately, due to the threading model of the golang runtime, this can not be encapsulated inside the docker daemon itself, but will have to be done for the invocation of the docker daemon.

The two ways to go about this is either with the unshare(1) utility or editing the systemd unit file for the docker.server (where ever this applies).

For systemd unit file (likely /usr/lib/systemd/system/docker.service, but may also be /etc/systemd/system/docker.service), include &#8220;`MountFlags=private`&#8221; in the `[service]` section. e.g.:
  
`<br />
[Unit]<br />
Description=Docker Application Container Engine<br />
Documentation=http://docs.docker.com<br />
After=network.target docker.socket<br />
Requires=docker.socket`

[Service]
  
ExecStart=/usr/bin/docker -d -H fd:// -H unix://var/run/docker.sock
  
MountFlags=private
  
LimitNOFILE=1048576
  
LimitNPROC=1048576

[Install]
  
WantedBy=multi-user.target

For the unshare(1) approach, you&#8217;ll have to find and edit the init script for your system. But the layout is as follows (and covers manually calling docker as well):
  
````````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
``````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
`````` 
  
This is the unshared PID we started earlier.

Notice also, we can determine for sure they are on different mount namespaces by checking the following:
  
```````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
``````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
`````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
````**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
```**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
``**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
`**Audience**:
  
This article is intended for folks familiar with docker and looking to fix particular issues encountered when using devicemapper storage/graph driver.

**Overview**:
  
While this is issue not exclusive to devicemapper, the mechanics currently involved in this driver cause it to be affected by this.

A couple of the more commons issues seen when using the &#8216;devicemapper&#8217; storage driver is when trying to stop and/or remove a contianer.
  
In the docker daemon logs, you may see output like:
  
` 
  
or more likely:
  
`` 

**Diagnosis**:
  
What&#8217;s happening behind the scenes is that devicemapper has established a new thin snapshot device to mount then container on.
  
Sometime during the life of that container another PID on the host, unrelated to docker, has likely started and unshared some namespaces from the root namespace, namely the mount namespace (CLONE_NEWNS). In the mounts referenced in this unshared host PID, it includes the thin snapshot device and its mount for the container runtime.

When the container goes to stop and unmount, while it may unmount the device from the root namespace, that umount does not propogate to the unshared host PID.
  
When the container is removed, devicemapper attempts to remove the thin snapshot device, but since the unshared host PID includes a reference to the device in its mountinfo, the kernel sees the device as still busy (EBUSY). Despite the fact the mounts of the root mount namespace may no longer show this device and mount.

**Reproduction**:
  
1) start the docker daemon (with debugging and forced devicemapper): \``sudo docker -d -D -g devicemapper`\`
  
2) start a container: \``sudo docker run -it busybox top`\`
  
3) run a pid with unshared mount namespace:
  
3.a) compile a simple C application: <a href="http://pastebin.com/HfSn8udJ" target="_blank">http://pastebin.com/HfSn8udJ</a>
  
3.b) use the unshare(1) utility: \``sudo unshare -m top`\`
  
4) stop or kill the container from step #2
  
5) watch the docker daemon logs

If you kill/quit the unshared application from #3 while the docker daemon is attempting to remove the container, then the daemon can clean up the container nicely. Otherwise there is cruft left around (in /var/lib/docker) and the daemon will not have record of the container to be removed.

**Investigating**:
  
Tooling to visualize this inheritance does not really exist yet, so deriving the culprit can take a little effort.
  
Sometimes the \``perf`\` tool can quickly point out the culprit. Something like:
  
``` 

Another option, is that while the container is running, get the mountinfo of the container from only the docker daemon&#8217;s pid:
  
```` 
  
Kill/stop the container (\``docker kill ...`\` or just &#8216;q&#8217; out of \`top\`).
  
Now that the container and device should be unmounted everywhere, lets grep for any PIDs still holding a reference:
  
````` 
  
We have our culprit. Find out the command:
  
`````` 
  
This is the unshared PID we started earlier.

Notice also, we can determine for sure they are on different mount namespaces by checking the following:
  
``````` 
  
Notice they are referencing different numbers here.

Now to cleanup, we can stop/kill this unshared PID, and \``docker rm ...`\` our test container.

**Solution**:
  
The solution to this issue is to have the docker docker itself run in an unshared mount namespace. Unfortunately, due to the threading model of the golang runtime, this can not be encapsulated inside the docker daemon itself, but will have to be done for the invocation of the docker daemon.

The two ways to go about this is either with the unshare(1) utility or editing the systemd unit file for the docker.server (where ever this applies).

For systemd unit file (likely /usr/lib/systemd/system/docker.service, but may also be /etc/systemd/system/docker.service), include &#8220;`MountFlags=private`&#8221; in the `[service]` section. e.g.:
  
`<br />
[Unit]<br />
Description=Docker Application Container Engine<br />
Documentation=http://docs.docker.com<br />
After=network.target docker.socket<br />
Requires=docker.socket`

[Service]
  
ExecStart=/usr/bin/docker -d -H fd:// -H unix://var/run/docker.sock
  
MountFlags=private
  
LimitNOFILE=1048576
  
LimitNPROC=1048576

[Install]
  
WantedBy=multi-user.target

For the unshare(1) approach, you&#8217;ll have to find and edit the init script for your system. But the layout is as follows (and covers manually calling docker as well):
  
```````` 
  
The important piece being the &#8220;`-m`&#8221; flag for cloning the mount namespace, and the &#8220;`--`&#8221; for the separation before arguments to the docker executable itself.

What this will accomplish is at the launch of the docker daemon, it will get its own private mount namespace that is a clone of the root namespace at that point. The docker daemon is free to create devices, mount them, unmount them and remove the device, because none of that mount information will be in the root mount namespace nor subject to being cloned into other PID&#8217;s mount namespace.

**Links**:

  * <a href=" https://github.com/docker/docker/issues?q=is%3Aopen+is%3Aissue+label%3A%2Fsystem%2Fdevicemapper" target="_blank">Current docker issues with the &#8216;devicemapper&#8217; label</a>
  * <a href="http://linux.die.net/man/2/unshare" target="_blank">unshare(2) &#8212; CLONE_NEWNS</a>
  * <a href="http://linux.die.net/man/3/errno" target="_blank">errno(3) &#8212; EBUSY</a>