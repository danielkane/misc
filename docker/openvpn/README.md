# Run the following in the host server the container is running in

# Edit the sysctl.conf file
sudo nano /etc/sysctl.conf

# Add the following line to the file
net.ipv4.ip_forward = 1

# Save and close the file, then run the following command to apply the changes
sudo sysctl -p



# Enable IP forwarding
sudo nano /etc/sysctl.conf
# Add the following line to the file
net.ipv4.ip_forward = 1
# Save and close the file, then run the following command to apply the changes
sudo sysctl -p

# Set up NAT for the VPN subnet
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/8 -o eth0 -j MASQUERADE

# Allow OpenVPN traffic in UFW
sudo ufw allow 1194/udp
sudo ufw allow OpenSSH

# Enable packet forwarding in UFW
sudo nano /etc/default/ufw
# Change the following line in the file
DEFAULT_FORWARD_POLICY="ACCEPT"
# Save and close the file

# Restart UFW to apply the changes
sudo ufw disable
sudo ufw enable