---
layout: post
title: "Don't loose your SSH connection anymore"
categories:
    - SysAdmin
tags:
    - SSH
---
When applying some sensitive changes to a remote server such as the network configuration, there is always a risk of loosing the SSH connection you're actually using, cutting off the branch you're sitting on...

In this blog post, I will talk about a few tips to avoid as much as possible these unpleasant situations.

## Checking your ability to ssh back before disconnecting

A common mistake is to disconnect from an SSH connection after some configuration changes but before having checked that the SSH communication can still be established. Take the time to SSH back from another terminal to the remote server to validate your new configuration before closing your current connection.

## Using a watchdog

Let's consider the following example: you are modifying the firewall rules on a server you are connected to and all your modifications are not persistent because you want to test them first. A watchdog within this context could be to reboot the server after a time-out to rollback all your changes and get back your hypothetically lost SSH connection:

    # As root
    sleep 1800 && reboot &

It will reboot the server if we don't stop it within the 30 minutes after the execution of the command. One way to stop it is to type `fg` then `CTRL+C`.

Of course, we want to be sure to stop it before it reboots the server if we haven't lost our SSH connection in the meantime. We can broadcast a message to remind us to stop it on time:

    for i in {0..1800..100}
    do
      if [[ $i -eq 1800 ]]
      then
        echo "Watchdog - Rebooting..." | wall
        reboot
      else
        echo "Watchdog - $((1800 - i)) seconds remaining" | wall
        sleep(100)
      fi
    done

The above loop will act as a reminder, broadcasting the remaining time before the watchdog reboots the server.

##
