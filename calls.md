# systemd DHCP code notes

UML class diagram:

Files:

  src/libsystemd-network/sd-dhcp-client.c
  src/network/networkd-link.c
  src/network/networkd-manager.c
  src/libsystemd-network/dhcp-internal.h
  src/libsystemd-network/dhcp-packet.c
  src/libsystemd-network/dhcp-protocol.h
  src/libsystemd-network/test-dhcp-client.c
  src/libsystemd-network/test-dhcp-option.c

Configuration variables related to the Anonymity Profile (AP)

`src/network/networkd-network-gperf.gperf`

    DHCP.ClientIdentifier,                  config_parse_dhcp_client_identifier,            0,                             offsetof(Network, dhcp_client_identifier)
    DHCP.UseDNS,                            config_parse_bool,                              0,                             offsetof(Network, dhcp_use_dns)
    DHCP.UseNTP,                            config_parse_bool,                              0,                             offsetof(Network, dhcp_use_ntp)
    DHCP.UseMTU,                            config_parse_bool,                              0,                             offsetof(Network, dhcp_use_mtu)
    DHCP.UseHostname,                       config_parse_bool,                              0,                             offsetof(Network, dhcp_use_hostname)
    DHCP.UseDomains,                        config_parse_dhcp_use_domains,                  0,                             offsetof(Network, dhcp_use_domains)
    DHCP.UseRoutes,                         config_parse_bool,                              0,                            offsetof(Network, dhcp_send_hostname)
    DHCP.Hostname,                          config_parse_hostname,                          0,                          offsetof(Network, dhcp_critical)
    DHCP.VendorClassIdentifier,             config_parse_string,                            0,       
