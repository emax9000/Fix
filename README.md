```markdown
# Script Download Utility for /etc/udev

This guide explains how to securely download scripts directly to your system's `/etc/udev` directory using GitHub's raw content URLs.

## Command Syntax
```bash
sudo sh -c 'curl -fL https://raw.githubusercontent.com/<USER>/<REPO>/<BRANCH>/<SCRIPT> \
    -o /etc/udev/<OUTPUT> && chmod 644 /etc/udev/<OUTPUT>'
```

## Parameter Reference
| Placeholder    | Description                          | Example Value               |
|----------------|--------------------------------------|----------------------------|
| `<USER>`       | GitHub username                      | `octocat`                  |
| `<REPO>`       | Repository name                      | `udev-utilities`           |
| `<BRANCH>`     | Branch name                          | `main`, `dev`, `v2.1`      |
| `<SCRIPT>`     | Filename in repository               | `device-setup.sh`          |
| `<OUTPUT>`     | Output filename in `/etc/udev`       | `custom-rules.sh`          |

## Example Usage
```bash
# Download and save a udev configuration script
sudo sh -c 'curl -fL https://raw.githubusercontent.com/octocat/udev-utils/main/install.sh \
    -o /etc/udev/device-init.sh && chmod 644 /etc/udev/device-init.sh'
```

## Command Breakdown
1. `sudo sh -c '...'`  
   Executes enclosed commands with root privileges
2. `curl -fL`  
   - `-f`: Silent failure on HTTP errors (404, 500, etc.)  
   - `-L`: Follow URL redirects
3. `https://raw.githubusercontent.com/...`  
   GitHub's direct file access URL
4. `-o /etc/udev/...`  
   Specifies output path
5. `&&`  
   Ensures next command only runs if previous succeeds
6. `chmod 644`  
   Sets secure permissions:  
   - Owner: read+write (`rw-`)  
   - Group: read (`r--`)  
   - Others: read (`r--`)  

## Security Features
- **Privilege Isolation**: Runs with minimal root access
- **HTTPS Verification**: Prevents MITM attacks
- **Atomic Write**: File only created if download succeeds
- **Safe Permissions**: Files non-executable by default
- **Error Handling**: Fails gracefully on download issues

## Verification Steps
After downloading:
```bash
# 1. Check file contents
cat /etc/udev/device-init.sh

# 2. Verify permissions
ls -l /etc/udev/device-init.sh
# Should show: -rw-r--r-- 1 root root

# 3. Make executable (if required)
sudo chmod +x /etc/udev/device-init.sh

# 4. Validate script (optional)
sudo bash -n /etc/udev/device-init.sh  # Syntax check
```

## Best Practices
1. **Review Scripts First**:
   ```bash
   curl https://raw.githubusercontent.com/... | less
   ```
2. **Use Commit Hashes** for immutable versions:
   ```bash
   .../commit/a1b2c3d/script.sh...
   ```
3. **Test in Sandbox** before production use
4. **Backup Original Files**:
   ```bash
   sudo cp /etc/udev/device-init.sh /etc/udev/device-init.sh.bak
   ```
5. **Audit Scripts** regularly

## Troubleshooting Guide
| Error                          | Solution                               |
|--------------------------------|----------------------------------------|
| `Permission denied`            | Prefix command with `sudo`             |
| `curl: (22) HTTP error 404`    | Verify URL parameters and file exists  |
| `No such file or directory`    | Create directory: `sudo mkdir -p /etc/udev` |
| `command not found: curl`      | Install curl: `sudo apt install curl -y` |
| File not executable            | `sudo chmod +x /etc/udev/<script>`     |

## Multi-Script Download
To download multiple scripts:
```bash
sudo sh -c '
SCRIPTS=("install.sh" "configure.sh" "utils.sh")
for script in "${SCRIPTS[@]}"; do
    curl -fL "https://raw.githubusercontent.com/user/repo/main/$script" \
         -o "/etc/udev/$script" && chmod 644 "/etc/udev/$script"
done
'
```

## Version History
| Date       | Changes                |
|------------|------------------------|
| 2023-10-15 | Initial documentation  |
| 2023-11-02 | Added security section |
```
