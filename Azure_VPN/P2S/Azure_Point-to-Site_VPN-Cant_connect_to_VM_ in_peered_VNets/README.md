# Azure Point-to-Site VPN - cannot connect to virtual machines in peered VNets - Need to check:

    If you have enabled Vnet peering after the P2S configuration and are using a windows machine, then the VPN client must be downloaded again for the peered Vnet routes to be propagated to your local machine.
    Please refer : https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing#multipeered

    In your case, since you are using the VPN client from MS store, you need to download the P2S VPN client profile from the Azure portal (VPN gateway > Point-to-site configuration > Download VPN client) and import/replace the new VpnSettings.xml file on the VPN client in your machine. This will make sure that the routes for your peered Vnets are included in your client profile and then you will be able to access the peered Vnet.
    NOTE : Make sure to take a back-up of your existing VPN client profile before replacing it with the new XML file for any future issues.

    To download the VPN client profile and import the XML file, please refer the below docs:
    https://docs.microsoft.com/en-us/azure/vpn-gateway/about-vpn-profile-download
    https://docs.microsoft.com/en-us/azure/vpn-gateway/openvpn-azure-ad-client#import
        