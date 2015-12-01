#configure-VPN-VYOS

####1. SITE TO SITE VYOS:
configure           
set vpn ipsec ipsec-interfaces interface eth0              

set vpn ipsec ike-group IKE-1W proposal 1            
set vpn ipsec ike-group IKE-1W proposal 1 encryption aes128             
set vpn ipsec ike-group IKE-1W proposal 1 hash sha1               
set vpn ipsec ike-group IKE-1W proposal 1 dh-group 5              
set vpn ipsec ike-group IKE-1W proposal 2                  
set vpn ipsec ike-group IKE-1W proposal 2 encryption aes128              
set vpn ipsec ike-group IKE-1W proposal 2 hash sha1                 
set vpn ipsec ike-group IKE-1W proposal 2 dh-group 2                     
set vpn ipsec ike-group IKE-1W lifetime 3600              
show vpn ipsec ike-group IKE-1W 

set vpn ipsec esp-group ESP-1W proposal 1             
set vpn ipsec esp-group ESP-1W proposal 1 encryption aes128             
set vpn ipsec esp-group ESP-1W proposal 1 hash sha1                  
set vpn ipsec esp-group ESP-1W proposal 2               
set vpn ipsec esp-group ESP-1W proposal 2 encryption 3des                    
set vpn ipsec esp-group ESP-1W proposal 2 hash md5               
set vpn ipsec esp-group ESP-1W lifetime 1800                       
set vpn ipsec esp-group ESP-1W pfs dh-group5                          
show vpn ipsec esp-group ESP-1W                    

set vpn ipsec site-to-site peer 1.103.2.42 authentication mode pre-shared-secret                       
edit vpn ipsec site-to-site peer 1.103.2.42                
set authentication pre-shared-secret secret                       
set default-esp-group ESP-1W                         
set ike-group IKE-1W                     
set local-address   192.168.0.5                        
set tunnel 1 local prefix 192.168.0.0/24                   
set tunnel 1 remote prefix 192.168.1.0/24                  
set authentication remote-id 192.169.0.3                   
commit                 
save                  
exit                 

#### 2. Remote-Access configuration VYOS (OpenVPN ) :

**1 Server Side**

configure          
set interfaces openvpn vtun0          
set interfaces openvpn vtun0 mode server          
set interfaces openvpn vtun0 server subnet 192.168.200.0/24          
>NOTE : Give the subnet ID different , so that it won't collapse with existing subnet Id     

set interfaces openvpn vtun0 tls cert-file /config/auth/server.crt        
set interfaces openvpn vtun0 tls dh-file /config/auth/dh.pem           
set interfaces openvpn vtun0 tls key-file /config/auth/server.key             
set interfaces openvpn vtun0 tls ca-cert-file /config/auth/ca.crt             

set interfaces openvpn vtun0 server push-route 35.0.0.0/24             
set interfaces openvpn vtun0 openvpn-option "--client-cert-not-required --script-security 3 --auth-user-pass-verify /usr/share/vyos-oc/auth_pam.pl      via-file"            

commit           
save             
exit           

**2 client side**

>Note :             
>> 1 First install openvpn          
2 Copy ca.art from server and copy to /etc/openvpn/          
3.Create client.conf , and add below lines, save it to /etc/openvpn/
     

auth-user-pass                    
remote-cert-tls server             
ca /etc/openvpn/ca.crt              
remote 192.168.20.134             
client            
dev tun               
proto udp              
resolv-retry infinite              
nobind                 
persist-key              
persist-tun               
verb 3              

Now run on client side following command                
**openvpn ­­config /etc/openvpn/client.conf**



#### 3.  Remote-­Access configuration VYOS (L2TP/IPsec) :

Check PDF
