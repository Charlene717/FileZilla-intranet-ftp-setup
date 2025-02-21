# FileZilla Server – Setting Up an FTP Service on a Local (Intranet) Network
[中文版連結](./README.md)

This guide demonstrates how to configure **FileZilla Server** (version 1.x or later) on a **Windows** system to set up a straightforward FTP service accessible within your institution’s or campus’s local network (same subnet). It does **not** rely on Windows system credentials, but uses FileZilla Server’s own user accounts for simplicity and reduced permission complexity.

---
  
  ## Table of Contents
  
  1. [Introduction](#introduction)  
    2. [Download and Install FileZilla Server](#1-download-and-install-filezilla-server)  
      3. [Launch and Verify Server](#2-launch-and-verify-filezilla-server)  
        4. [Create a User (Custom Password)](#3-create-a-user-custom-password-in-filezilla-server)  
          5. [Configure Mount Points (Shared Folder)](#4-configure-mount-points-shared-folder)  
            6. [Allow Port 21 in Windows Firewall](#5-allow-port-21-in-windows-firewall)  
              7. [Local Testing (127.0.0.1)](#6-local-testing-127001)  
                8. [Testing from Another PC on the Same Network](#7-testing-from-another-pc-on-the-same-network)  
                  9. [FAQ and Notes](#faq-and-notes)  
                    10. [Conclusion](#conclusion)
                      
                      ---
                        
                        ## Introduction
                        
                        Many schools or organizations use internal networks (Intranet) that do not require external access. **FileZilla Server** offers a simple way to share files via FTP. By using FileZilla Server’s built-in user management (rather than Windows credentials), you can streamline permissions and reduce complexity.
                      
                      ---
                        
                        ## 1. Download and Install FileZilla Server
                        
                        1. **Get the installer**  
                        - Visit the [FileZilla official website](https://filezilla-project.org/download.php?type=server) and download **FileZilla Server** (not FileZilla Client).
                      
                      2. **Run the installer**  
                        - Accept the default settings if you have no special requirements.  
                      - After installation, you will have:
                        - **FileZilla Server service** (a Windows service)  
                      - **FileZilla Server Interface** (administration interface)
                      
                      ---
                        
                        ## 2. Launch and Verify FileZilla Server
                        
                        1. **Open the Administration Interface**  
                        - After installation, run **FileZilla Server Interface**. It will connect to the local service by default (e.g., `127.0.0.1:14148`).
                      
                      2. **Check if Port 21 is Listening**  
                        - Open Command Prompt (CMD) and enter:
                        ```bash
                      netstat -ano | findstr :21
                      ```
                      - If you see `0.0.0.0:21 LISTENING` or `[::]:21 LISTENING`, it means FileZilla Server is awaiting FTP connections on port 21.
                      
                      ---
                        
                        ## 3. Create a User (Custom Password) in FileZilla Server
                        
                        1. **Access the Users configuration**  
                        - In the top menu:  
                        ```
                      Server → Configure
                      ```
                      - On the right side under **“Select a page”**, choose **Rights management / Users**.
                      
                      2. **Add a new user**  
                        - In *Available users*, click **Add**.  
                      - Enter a username (for example, `TestUser`), then click **OK**.  
                      - A settings panel (with tabs like `General`, `Filters`, `Limits`) will appear on the right.
                      
                      3. **Set “Require a password to log in”**  
                        - In the `General` tab:  
                        - Ensure **User is enabled** is checked.  
                      - Under **Authentication**, select **Require a password to log in**.  
                      > **Do not** select **Use system credentials to log in**, or the server will attempt to use Windows accounts.  
                      - Once selected, a text field (labeled “Leave empty to keep existing password”) should appear. Enter your desired password (e.g., `MySecret123`).
                      
                      ---
                        
                        ## 4. Configure Mount Points (Shared Folder)
                        
                        1. **Mount points**  
                        - In the *Mount points* area, click **Add**.  
                      - **Virtual path**: typically use `/` to represent the root directory the user sees upon login.  
                      - **Native path**: the actual Windows folder path you want to share (e.g., `D:\FTP_Folder`).
                      
                      2. **Assign Access Mode**  
                        - Check `Read` + `Write` (if uploads are permitted), or just `Read` if downloads only.  
                      - Additional options:
                        - *Apply permissions to subdirectories*  
                        - *Writable directory structure*  
                        - *Create native directory if it does not exist*  
                        
                        3. **Save**  
                        - Click **OK** or **Apply**. You should now see an entry like `"/" → D:\FTP_Folder` in *Mount points*.
                      
                      ---
                        
                        ## 5. Allow Port 21 in Windows Firewall
                        
                        1. **Open Windows Defender Firewall (Advanced Security)**  
                        - Search in the Start menu for **Windows Defender Firewall with Advanced Security**.
                      
                      2. **Create an Inbound Rule**  
                        - On the left, choose **Inbound Rules**; on the right, click **New Rule...**  
                        - Select **Port** → choose `TCP` → enter `21` → **Allow the connection**.  
                      - Specify applicable profiles (Domain / Private / Public). Name the rule (e.g., `Allow FTP 21`).
                      
                      3. **(If using Passive Mode)**  
                        - Go to `Protocols settings → FTP and FTP over TLS (FTPS)` in FileZilla Server, configure a passive port range (e.g., `50000-50100`), and create a corresponding inbound rule for these ports.  
                      - If you only need Active mode on a simple intranet, no extra steps are required.
                      
                      ---
                        
                        ## 6. Local Testing (127.0.0.1)
                        
                        1. **Install or open FileZilla Client on the same machine**  
                        - Note that **FileZilla Client** and **FileZilla Server** are two separate applications.
                      
                      2. **Quickconnect**  
                        - Host: `127.0.0.1`  
                      - Username: `TestUser` (the user you created)  
                      - Password: `MySecret123`  
                      - Port: `21`  
                      - Click **Quickconnect**.
                      
                      3. **Check the result**  
                        - If successful, you should see the contents of `D:\FTP_Folder`.  
                      - If it fails, verify you did not enable **Use system credentials** and confirm your firewall and user/password settings are correct.
                      
                      ---
                        
                        ## 7. Testing from Another PC on the Same Network
                        
                        1. **Find the server’s local IP**  
                        - On the server machine, run `ipconfig` in CMD to locate an IP like `192.168.x.x` or `10.x.x.x`.
                      
                      2. **Use FileZilla Client on another PC**  
                        - Host: the server’s IP (e.g., `192.168.50.52`)  
                      - Username: `TestUser`  
                      - Password: `MySecret123`  
                      - Port: `21`  
                      - Click **Quickconnect**.
                      
                      3. **If it fails**  
                        - Try `ping 192.168.x.x` first to ensure basic network connectivity.  
                      - If ping is successful, a firewall or security software might be blocking the FTP connection.  
                      - If ping fails, the PCs may be on different VLANs, or a higher-level network firewall might be blocking traffic.
                      
                      ---
                        
                        ## FAQ and Notes
                        
                        1. **“501 Couldn't open directory” or unable to list contents**  
   - Make sure `/` → `D:\FTP_Folder` is defined in *Mount points* and that `Read` is checked.  
   - Windows NTFS permissions must also allow the FileZilla Server process to access that folder.

2. **External (Internet) Access**  
   - You would need port forwarding on your router or institutional firewall, plus a public IP or proper DNS.  
   - Consult your network admin for details.

3. **Use FTPS or SFTP for Encryption**  
   - Standard FTP transmits credentials in plain text.  
   - Consider **FTP over TLS (FTPS)** or **SFTP** (requires SSH, typically on port 22) for improved security.

---

## Conclusion

1. **Install FileZilla Server and verify Port 21**  
2. **Create a user (require password, avoid system credentials)**  
3. **Configure Mount Points (`"/"` → `D:\FTP_Folder`) with proper read/write settings**  
4. **Allow Port 21 in Windows Firewall**  
5. **Test locally (`127.0.0.1`), then across the intranet using the server’s IP**  

By following these steps, you can quickly establish a functioning FTP file-sharing service on your local network using FileZilla Server. For external access, please address additional security concerns (FTPS, SFTP) and consult your network team on port forwarding or firewall rules.

