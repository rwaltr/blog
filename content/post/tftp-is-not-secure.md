---
title: TFTP is NOT a secure protocol… but it was never designed to be.
#subtitle: I am starting to see...
date: 2018-07-07
tags: ["software", "tech"]
---

TFTP, or Trivial File Transfer Protocol is designed to be… trivial. It has no authentication, authorization, or any fancy abilities such as listing the directory of files you are looking at.

TFTP was created as a “streamlined” way to send and receive files from devices, especially devices with little to “no” compute resources. For example; one common use is to load firmware and software packages to Cisco devices using an internally managed repository on a TFTP server. 

Perhaps the most common use of TFTP is for PXE booting. Because devices that are booting with PXE did not have a chance to retrieve any credentials and are typically performed by embedded silicon on the NIC of the device. The streamlined nature of TFTP shines in this role. 

TFTP servers should not be exposed to the Internet, If you must, ensure that the data on this server is allowed for public viewing and separated from your internal environment
