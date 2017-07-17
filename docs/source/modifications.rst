Summary of systemd modifications for the Anonymity Profiles
=============================================================

Option A
--------

1. Add ``UseAnonymityProfile`` configuration variable::

     src/network/networkd-network-gperf.gperf

     DHCP.UseAnonymityProfile, config_parse_bool, 0, offsetof(Network,
     dhcp_use_anonymity_profile)

2. Add ``dhcp_use_anonymity_profile`` variable and
   ``network_apply_anonymity_profile_if_set`` function::

     src/network/networkd-network.h

     bool dhcp_use_anonymity_profile;

     int network_apply_anonymity_profile_if_set(Network *network);

3. Implement function ``network_apply_anonymity_profile_if_set``::

    src/network/networkd-network.c

    /* RFC7844*/
    int network_apply_anonymity_profile_if_set(Network *network) {
        if (network->dhcp_use_anonymity_profile) {
                /* RFC7844 3.7
                   SHOULD NOT send the Host Name option */
                network->dhcp_send_hostname = false;
                /* RFC 7844 3:
                   MAY contain the Client Identifier option
                   Section 3.5:
                   clients MUST use client identifiers based solely
                   on the link-layer address */
                network->dhcp_client_identifier = DHCP_CLIENT_ID_MAC;
                /* RFC 7844 3.10:
                   SHOULD NOT use the Vendor Class Identifier option */
                network->dhcp_vendor_class_identifier = NULL;
                /* RFC 7844 3:
                   SHOULD NOT contain any other option. */
                network->dhcp_use_mtu = false;
                network->dhcp_use_routes = false;

                network->dhcp_use_timezone = false;
                /* FIXME RFC7844: check if the following options are needed */
                network->dhcp_use_ntp = false;
                network->dhcp_use_dns = false;
                network->dhcp_use_domains = false;
                /* FIXME: check options for ipv6 */
                // network->ipv6_privacy_extensions = IPV6_PRIVACY_EXTENSIONS_NO;
        }
        return 0;
    }


Unordered parts of code modified/to modify
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    src/network/networkd-dhcp4.c

    if (!link->network->dhcp_use_anonymity_profile) {
      r = sd_dhcp_client_set_request_option_defaults(link->dhcp_client);

    src/systemd/sd-dhcp-client.h

    int sd_dhcp_client_set_request_option_defaults(
            sd_dhcp_client *client);

    src/libsystemd-network/sd-dhcp-client.c

    int sd_dhcp_client_set_request_option_defaults(sd_dhcp_client *client) {

    // FIXME RFC788: set this here instead of
    // sd_dhcp_client_set_request_option_defaults? (defined here and called in networkd-dhcp4.c)
    // bool anonymity_profile;

    /* RFC2131 section 3.5:
       in its initial DHCPDISCOVER or DHCPREQUEST message, a
       client may provide the server with a list of specific
       parameters the client is interested in. If the client
       includes a list of parameters in a DHCPDISCOVER message,
       it MUST include that list in any subsequent DHCPREQUEST
       messages.
     */
    /* RFC7844: parameter request list is not set now by default,
       so it must be checked that there are actually options. */
    if(client->req_opts_size > 0) {
            r = dhcp_option_append(

    /* FIXME RFC7844: there should not be a REBOOT state */

    /* RFC7844 section 3
       SHOULD NOT contain any other option.
       Link->Network->dhcp_use_anonymity_profile is already set here,
       but client struct does not have this field
       The code to set default options for PARAMETER_REQUEST_LIST
       is moved to a function */

    src/network/networkd-link.c

    r = sd_dhcp_client_start(link->dhcp_client);

    src/network/networkd-manager.c

    src/libsystemd-network/dhcp-internal.h

    src/libsystemd-network/dhcp-packet.c

    src/libsystemd-network/dhcp-protocol.h

    src/libsystemd-network/test-dhcp-client.c

    src/libsystemd-network/test-dhcp-option.c

    src/?/sd-dhcp-lease.c
