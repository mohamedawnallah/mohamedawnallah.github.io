---
title: "What struggle did I overcome this week?"
excerpt: "This week, I struggled with Egypt's unpredictable internet. Here is how I overcame it!"
show_date: true
categories:
  - Blog
tags:
  - Internet
  - Automation
  - Productivity
  - Apple Script
header:
    overlay_image: https://img.freepik.com/free-vector/no-connection-concept-illustration_114360-5987.jpg
    overlay_filter: 0.5
    caption: "Photo credit: [**Freepik**](https://freepik.com)"
    teaser: https://img.freepik.com/free-vector/no-connection-concept-illustration_114360-5987.jpg
toc: true
toc_sticky: true
breadcrumbs: true
---

## Introduction
This week, I struggled with Egypt's unpredictable internet. As a Data Engineer, my work heavily relies on connectivity, making the frequent disruptions frustrating. I had a simple requirement: a reliable internet connection with a minimum of 1mbps actual download speed and absolutely no downtime.

## The Challenge: Unpredictable Internet
Before delving into the solution, let's first understand the challenges presented by Egypt's internet landscape:
1. **Wireless Cellular Networks:** These networks were dependable but came with a higher price tag. While they offered consistent connectivity, the cost was a significant concern.
2. **Wired Networks:** On the other hand, Wired Networks were more cost-effective, but their reliability left much to be desired. Frequent disruptions were the norm, and I needed a solution to manage these interruptions effectively.

## The Solution: ConnectMeNow
That's why I built ConnectMeNow. It's an open-source project available on GitHub. It streamlines Wi-Fi network management for macOS users, making it easy to switch between networks seamlessly and prioritize connectivity options. Here's how ConnectMeNow addresses these challenges:
1. **Prioritizing Networks:** ConnectMeNow prioritizes the more budget-friendly wired network(s) as the primary choice and designates the reliable cellular network(s) as the backup option. This ensures you remain connected while keeping costs in check.
2. **Customizable Network List:** Users have the flexibility to tailor their list of main and backup network SSIDs within the script, allowing it to align with their network preferences.
3. **Password Management:** ConnectMeNow securely manages Wi-Fi passwords using macOS's Keychain, offering a safe and efficient method for accessing networks.

Here's a snippet of the script built using AppleScript you can find in the ConnectMeNow project:
```applescript
property mainNetworkSSIDs : {"New_SSID_1", "New_SSID_2"} -- Modify the main network SSIDs here
property backupNetworkSSIDs : {"New_SSID_3"} -- Modify the backup network SSIDs here

on connectToNetwork(networkSSID)
    set passwordQuoted to quoted form of getPasswordFromKeychain(networkSSID)
    if passwordQuoted is not missing value then
        if networkExists(networkSSID) then
			set currentNetworkSSIDQuoted to quoted form of currentNetworkSSID()
			set networkSSIDQuoted to quoted form of networkSSID
            if currentNetworkSSIDQuoted is not equal to networkSSIDQuoted then
                do shell script "/usr/sbin/networksetup -setairportnetwork en0 " & networkSSIDQuoted & " " & passwordQuoted
            end if
            return true
        else
            return false
        end if
    else
        return false
    end if
end connectToNetwork

on getPasswordFromKeychain(networkSSID)
    set keychainPassword to do shell script "/usr/bin/security find-generic-password -wa " & quoted form of networkSSID
    return keychainPassword
end getPasswordFromKeychain

on networkExists(networkSSID)
    set currentSSID to currentNetworkSSID()
    if currentSSID is networkSSID then
        return true
    else
        set networkList to do shell script "/System/Library/PrivateFrameworks/Apple80211.framework/Versions/A/Resources/airport -s"
        if networkList contains networkSSID then
            return true
        else
            return false
        end if
    end if
end networkExists


on currentNetworkSSID()
    set ssid to do shell script "/System/Library/PrivateFrameworks/Apple80211.framework/Versions/A/Resources/airport -I | awk -F': ' '/ SSID/ {print $2}'"
    return ssid
end currentNetworkSSID

on main()
    set mainNetworkConnected to false
    set backupNetworkConnected to false
    
    repeat with eachSSID in mainNetworkSSIDs
        if not mainNetworkConnected then
            set mainNetworkConnected to connectToNetwork(eachSSID)
        end if
    end repeat
    
    repeat with eachSSID in backupNetworkSSIDs
        if not mainNetworkConnected and not backupNetworkConnected then
            set backupNetworkConnected to connectToNetwork(eachSSID)
        end if
    end repeat
    
    if not mainNetworkConnected and not backupNetworkConnected then
        display notification "No suitable network found"
    end if
end main

main()
```

## How ConnectMeNow Saved the Day
With ConnectMeNow, I transformed my internet experience in Egypt. I could seamlessly switch between networks, ensuring I stayed connected without any downtime. And the best part? It cost me just approximately $6.50 USD per month.

In summary, ConnectMeNow emerged as the tech solution that rescued my work from Egypt's internet challenges. If you're a macOS user dealing with unreliable Wi-Fi networks, give ConnectMeNow a try, and you'll never have to worry about internet interruptions again.

[Explore ConnectMeNow on GitHub][connect-me-now]

[connect-me-now]: https://github.com/mohamedawnallah/ConnectMeNow
