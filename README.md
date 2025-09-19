# Setting a Fixed IP Address on Ubuntu Server (Using `50-cloud-init.yaml`)

This guide shows how to configure a static IP address on an Ubuntu Server (version 24.04.03 LTS) by modifying the `50-cloud-init.yaml` configuration file.

### Prerequisites:

* Ubuntu 24.04.03 LTS installed.
* Root or sudo privileges.

### Steps:

1. **Backup the Existing Configuration File:**

   Before making any changes, create a backup of the existing `50-cloud-init.yaml` file to easily revert if needed:

   ```bash
   sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bck
   ```

2. **Edit the `50-cloud-init.yaml` File:**

   The `50-cloud-init.yaml` file manages the network configuration when cloud-init is used. To configure a static IP, you will need to modify this file.

   Open the file using `nano`:

   ```bash
   sudo nano /etc/netplan/50-cloud-init.yaml
   ```

   Replace the contents with the following, adjusting the interface name (`ens34`), IP addresses, and gateway as needed:

   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       ens34:
         dhcp4: false
         addresses:
           - 192.168.1.16/24
         gateway4: 192.168.1.1
         nameservers:
           addresses:
             - 8.8.8.8
             - 8.8.4.4
   ```

   * **ens34**: Replace this with your actual network interface name. Use the `ip a` command to check the name if you're unsure.
   * **dhcp4: false**: Disables DHCP and enables static IP configuration.
   * **addresses**: Set the desired static IP address (e.g., `192.168.1.16/24`).
   * **gateway4**: Define your default gateway IP (e.g., `192.168.1.1`).
   * **nameservers**: List DNS servers (e.g., Google's DNS servers `8.8.8.8` and `8.8.4.4`).

3. **Apply the Changes:**

   After editing and saving the file (`Ctrl + O`, `Enter`, `Ctrl + X` to exit), apply the changes to the network configuration:

   ```bash
   sudo netplan apply
   ```

4. **Verify the IP Configuration:**

   To ensure the static IP address has been applied, run:

   ```bash
   ip a show ens34
   ```

   This will display the interface details, including the assigned IP address.

5. **Test Network Connectivity:**

   To verify that your server is correctly configured, you can test network connectivity:

   * Ping an external IP (e.g., Google's DNS):

     ```bash
     ping -c 4 8.8.8.8
     ```

   * Ping a domain name to check DNS resolution:

     ```bash
     ping -c 4 google.com
     ```

   If both commands succeed, your static IP configuration is correct.

---

### Troubleshooting:

* **Restoring the Backup**: If something goes wrong, you can restore the original configuration by copying the backup file back to its original location:

  ```bash
  sudo cp /etc/netplan/50-cloud-init.yaml.bck /etc/netplan/50-cloud-init.yaml
  sudo netplan apply
  ```

* **Check Network Interface**: If you’re unsure of the network interface name, use the `ip a` command to list all network interfaces.

---

### Conclusion:

By modifying the `50-cloud-init.yaml` file, you can easily configure a static IP address on your Ubuntu server. After saving the changes and applying them with `netplan apply`, your server should have a fixed IP address, ensuring reliable network connectivity.

---

