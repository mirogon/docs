# Debian based Distributions Documentation

DBD's = Debian Based Distributions

# Connect to Wifi

DBD's normally use a mixture of wpa_supplicant, /etc/network/interfaces, /etc/wpa_supplicant/* to connect to wifi<br>
This is low level an requires a good understanding of networking
It is easier and better to use either network-manager or wicd to connect to wifi<br>
These applications also manages auto connect after boot.

Recommendation: wicd

Uninstall network-manager, dhcpcd, other network managing programs/services and stop them

    #install needed packages/programs
    sudo apt install wicd-cli wicd-curses

    #add user to netdev group
    sudo adduser <username> netdev
    
    #reload dbus service
    sudo service dbus reload

    sudo service wicd start

    #start wict ui
    sudo wicd-curses

# Show installed libraries

    #show all
    ldconfig -p

    #show all installed sdl libs
    ldconfig -p | grep -i SDL*

# Mount Drive

To mount a drive<br>

    sudo mount [drive] [mounting_point]
<br>

example<br>

    sudo mount /dev/sda1 /media/m1smr/DEV

note: /media/m1smr/DEV has to exist

## Mount Drive read write

    sudo mount -o rw [device] [mounting_point]

## Mount NTFS drive with hibernation

To remove hibernation state and easily mount the drive use<br>

    sudo ntfsfix [device] <br>

after this, just mount it normally
