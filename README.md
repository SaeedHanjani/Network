interface sstp-client add name=sstp-out1 connect-to=80.191.11.217 user=user1 password=pass1 profile=default add-default-route=yes



### To configure an SSTP (Secure Socket Tunneling Protocol) tunnel between two MikroTik routers, follow these steps. SSTP is commonly used for VPN connections and works over HTTPS, providing security using SSL/TLS


#### Step 1: Ensure Both Routers Have Public IPs
Ensure that both MikroTik routers have public IP addresses (or at least the one acting as the server) and the necessary firewall/NAT rules to allow SSTP traffic.


#### Step 2: SSTP Server Configuration (on Router 1)
1. Configure Certificates:
SSTP uses SSL, which requires a certificate for secure communication. If you don't already have a valid certificate, you can generate a self-signed certificate or use a trusted CA.

Generate a Self-Signed Certificate:

```
/certificate add name=server-cert common-name=server-cert key-size=2048 days-valid=365 key-usage=key-cert-sign,crl-sign
/certificate sign server-cert
/certificate set server-cert trusted=yes
```

#### 2. Enable SSTP Server:

```
/interface sstp-server server set enabled=yes default-profile=default encryption=all certificate=server-cert
```
Ensure that you set default-profile=default or create a new profile that suits your needs.

#### 3. Add PPP User for SSTP Authentication:
Add a PPP secret user for authentication.


```
/ppp secret add name=user1 password=pass1 service=sstp profile=default
```

#### 4. Firewall Rule (Optional):
If you have strict firewall rules, allow SSTP traffic (port 443):

```
/ip firewall filter add chain=input protocol=tcp port=443 action=accept place-before=0
```

#### Step 3: SSTP Client Configuration (on Router 2)
1. Add the SSTP Client:
Configure the SSTP client to connect to the server's public IP.

```
/interface sstp-client add name=sstp-out1 connect-to=<SERVER_PUBLIC_IP> user=user1 password=pass1 profile=default add-default-route=yes
```
Replace <SERVER_PUBLIC_IP> with the public IP address of Router 1.

####2. Set Routing (Optional):
If you want to route all traffic through the SSTP tunnel, ensure you set the add-default-route=yes. Otherwise, set up specific routes to send only certain traffic through the tunnel.

Step 4: Check Connectivity
On the SSTP Server (Router 1), check if the client is connected:

```
/interface sstp-server print
```

On the SSTP Client (Router 2), verify the connection:

```
/interface sstp-client print
```
#### Step 5: Test the VPN Tunnel
After configuration, try pinging Router 1 from Router 2 to ensure the tunnel is functioning correctly. Use the internal IP addresses assigned to the routers through the SSTP connection.

Optional Configuration:
Route Specific Subnets: If you want to route specific subnets through the SSTP tunnel, configure routes accordingly on both routers.

```
/ip route add dst-address=<remote subnet> gateway=sstp-out1
```
Firewall and NAT Considerations: Ensure both routers have proper firewall and NAT configurations to handle the SSTP traffic without restrictions.

This will create an SSTP tunnel between two MikroTik routers, enabling secure communication over the internet.





