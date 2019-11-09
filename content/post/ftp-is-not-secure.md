---
title: FTP is NOT secure software.
#subtitle: I am starting to see...
date: 2018-07-01
tags: ["software", "tech"]
---

FTP is a protocol created in 1980 (RFC959) to transfer files to different servers and networks. typically over a WAN connection.

However, it is not a service you should use to host sensitive information. The following is why,

* FTP does not perform encryption on transit; Anyone between you and the FTP server can, will, and most likely have been monitoring and logging traffic.

* FTP does not securely communicate credentials; FTP does not encrypt authentication, this means that passwords are in plain text and can be READ BY ANYONE monitoring your network.

If your data should be used by the public and you do not need to authorize, FTP is a fine protocol, but it should not be used for anything that should not be public.

The major alternatives are SFTP and FTPS.

SFTP is included in most Linux operating systems and is part of SSH. Credentials and transmission are encrypted by default. *Nix systems include the SFTP client by default, For Windows you can use WinSCP, Putty’s SFTP, or Filezilla

FTPS is a TSL/SSL variant of FTP. Requiring x.509 certificates, however it does have more port requirements than SFTP and FTP and supports more legacy devices.

Happy Encryption!

~ R.Walt