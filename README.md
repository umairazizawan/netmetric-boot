# netmetric-boot

**netmetric-boot** is a simple tool that automatically sets interface metrics when `ifupdown` managed network interfaces come up on Linux systems, using the `ifmetric` utility. This ensures consistent routing priorities across multiple network interfaces (e.g., prioritizing Wi-Fi over Ethernet or vice versa).

## How It Works

The script automatically executed when any interface comes up. If the desired interface comes up, such as `eth0` or `wlan0`, it sets the metric accordingly using `ifmetric`. To follow these steps to use the script

***Please Note that this approach will not work if the interface is being managed by NetworkManager.
The reason is that each network management system—ifupdown and NetworkManager—has its own configuration and execution mechanism. They’re designed to be independent of one another.***

* Check the name of your network interface using the following command:

    ```
    ip route
    ```
    The output should be something like this

    ```
    default via 192.168.1.1 dev wlp6s0 proto dhcp src 192.168.1.10 metric 600 
    192.168.1.0/24 dev wlp6s0 proto kernel scope link src 192.168.1.10 metric 600
    ```
    In the above the name of the interface is `wlp6s0`

* Change the name of the interface in the apply_metric script. For the above output the new file will look as follows

    ```
    #!/bin/sh
    # /etc/network/if-up.d/set-metric
    METRIC_VALUE_WIFI=200


    if [ "$IFACE" = "wlp6s0" ]; then

        # Apply the metric value using ifmetric
        /usr/sbin/ifmetric $IFACE $METRIC_VALUE_LAN
    fi
    ```
    NOTE: You can also changed the applied metric value. Please keep in the mind that the lower the metric the higher the priority of the interface.

* Place the script in `/etc/network/if-up.d/`. It can be placed using following command

    ```
    sudo cp apply_metric /etc/network/if-up.d/
    ```
* Next, set the permissions of the script using the following command

    ```
    sudo chmod +x /etc/network/if-up.d/apply_metric 
    ```

    This command:
    
    * Uses chmod to change file permissions.

    * The +x flag makes the script executable.

* After this reboot the device and run the `ip route` command again. You should see the metric applied

    ```
    ```

## Requirements

* Linux system using traditional ifupdown networking (e.g., Debian, Ubuntu etc. without Netplan or NetworkManager)

* ifmetric utility. This can be installed using 
    ```
    sudo apt install ifmetric
    ```