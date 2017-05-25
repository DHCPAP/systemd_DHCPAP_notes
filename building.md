# Building systemd


## Install dependencies

    apt build-dep systemd

## Clone and compile systemd

    git clone XXX
    cd systemd
    ./autogen.sh
    ./configure CFLAGS='-g -O0 -ftrapv' --sysconfdir=/etc --localstatedir=/var --libdir=/usr/lib  --with-rootprefix=/ --with-rootlibdir=/lib
    make -j`nproc`

The generated binaries will be in .libs

## Hacky way to test new systemd-networkd binary

Check that the system binary is different to the generated binary

    sha256sum .libs/systemd-networkd /lib/systemd/systemd-networkd

To find required library:

    ldd .libs/systemd-networkd | grep system

To find which declarations are missing:

    readelf -Ws /lib/systemd/libsystemd-shared-233.so | grep config_parse_uint
    readelf -Ws .libs/libsystemd-shared-233.so | grep config_parse_uint

Configure network:

    cat /etc/systemd/network/20-dhcp.network
    [Match]
    Name=en*

    [Network]
    DHCP=ipv4

    [DHCP]
    UseAnonymityProfile=true

Replace system binaries with the compiled ones:

If Network Manager is running, stop it:

    systemctl stop NetworkManager.service

Restart

    systemctl restart systemd-networkd

To keep it running in the system:

To enable wifi interface:

    wpa_passphrase MyNetwork SuperSecretPassphrase > /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
    systemctl enable wpa_supplicant@wlan0.conf

To obtain debugging logs, add to the unit:

    [Service]                                                                      
    Environment=SYSTEMD_LOG_LEVEL=debug

## Scan DHCP packages

  /usr/sbin/tcpdump -r /tmp/dhcp-before.pcap  -X -n

## Tests

    make -j`nproc` check
