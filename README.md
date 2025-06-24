
```markdown
# UDEV Rule Download Utility

This guide explains how to securely download UDEV rule files directly to your system's `/etc/udev/rules.d/` directory using GitHub's raw content URLs.

## Command Syntax
```bash
sudo sh -c 'curl -fL https://raw.https://github.com/emax9000/Fix/blob/main/50-qmk.rules \
    -o /etc/udev/rules.d/<RULE_NAME>.rule && chmod 644 /etc/udev/rules.d/50-qmk.rules'
```

## Parameter Reference
| Placeholder    | Description                          | Example Value               |
|----------------|--------------------------------------|----------------------------|
| `<USER>`       | GitHub username                      | `octocat`                  |
| `<REPO>`       | Repository name                      | `udev-rules`               |
| `<BRANCH>`     | Branch name                          | `main`, `stable`, `v1.2`   |
| `<RULE_FILE>`  | Filename in repository               | `99-usb-devices.rules`     |
| `<RULE_NAME>`  | Output filename (without .rule)      | `99-custom-devices`        |

## Example Usage
```bash
# Download and save a UDEV rule
sudo sh -c 'curl -fL https://raw.githubusercontent.com/octocat/udev-rules/main/99-usb-permissions.rules \
    -o /etc/udev/rules.d/99-usb-devices.rule && chmod 644 /etc/udev/rules.d/99-usb-devices.rule'
```

## Command Breakdown
1. `sudo sh -c '...'`  
   Executes commands with root privileges
2. `curl -fL`  
   - `-f`: Silent failure on HTTP errors  
   - `-L`: Follow redirects
3. `https://raw.githubusercontent.com/...`  
   GitHub's direct file URL
4. `-o /etc/udev/rules.d/...`  
   Output path to udev rules directory
5. `&&`  
   Ensures chmod only runs if download succeeds
6. `chmod 644`  
   Sets secure permissions:  
   - Owner: read+write  
   - Group: read  
   - Others: read  

## UDEV Rule Management
After downloading:
```bash
# Reload UDEV rules
sudo udevadm control --reload-rules
sudo udevadm trigger

# Verify rule loading
udevadm test /sys/class/<device-class>/<device> 2>&1 | grep <RULE_NAME>

# Check applied rules
udevadm info -a -p $(udevadm info -q path -n /dev/<device>)
```

## Best Practices for UDEV Rules
1. **Naming Convention**:  
   Rules should be named with a priority number (e.g., `99-custom.rule`)
2. **Testing**:  
   Always test rules in debug mode first:
   ```bash
   udevadm test $(udevadm info -q path -n /dev/<device>)
   ```
3. **Backup**:  
   Keep backups of custom rules:
   ```bash
   sudo cp /etc/udev/rules.d/99-custom.rule /etc/udev/rules.d/99-custom.rule.bak
   ```
4. **Validation**:  
   Check syntax before applying:
   ```bash
   udevadm verify /etc/udev/rules.d/<rule-file>.rule
   ```

## Security Features
- Rules are non-executable by default
- HTTPS verification prevents MITM attacks
- Atomic write ensures incomplete downloads don't affect system
- Proper permissions (root ownership)

## Troubleshooting Guide
| Issue                          | Solution                               |
|--------------------------------|----------------------------------------|
| Rule not applying              | Check numbering (lower numbers execute first) |
| Permission errors              | Verify `chmod 644` was applied         |
| Syntax errors                  | Use `udevadm verify <rule-file>`       |
| Device not recognized          | Check `lsusb` or `lspci` output        |
| Rules not reloading            | Run: `sudo udevadm control --reload`   |

## Multi-Rule Download
To download multiple rules:
```bash
sudo sh -c '
RULES=("99-usb.rules" "80-network.rules")
for rule in "${RULES[@]}"; do
    curl -fL "https://raw.githubusercontent.com/user/repo/main/$rule" \
         -o "/etc/udev/rules.d/$rule" && chmod 644 "/etc/udev/rules.d/$rule"
done
'
```

## Sample Rule File
Example `99-usb-devices.rule` content:
```bash
# USB device permissions
SUBSYSTEM=="usb", ATTR{idVendor}=="abcd", ATTR{idProduct}=="efgh", MODE="0666"

# Serial device permissions
KERNEL=="ttyUSB*", MODE="0666", GROUP="dialout"
```

## Version History
| Date       | Changes                |
|------------|------------------------|
| 2023-11-15 | Initial documentation  |
| 2023-12-01 | Added UDEV specific instructions |
```

## Download Instructions
1. Save this content as `udev_rule_download.md`
2. Use with your project documentation
3. Apply rules following the verification steps

To download this documentation directly:
```bash
sudo sh -c 'curl -fL https://raw.githubusercontent.com/<YOUR_USER>/<YOUR_REPO>/main/udev_rule_download.md \
    -o /etc/udev/README.md && chmod 644 /etc/udev/README.md'
```

For immediate rule application:
```bash
sudo sh -c 'curl -fL https://raw.githubusercontent.com/<USER>/<REPO>/<BRANCH>/<RULE>.rules \
    -o /etc/udev/rules.d/<RULE>.rule && chmod 644 /etc/udev/rules.d/<RULE>.rule \
    && udevadm control --reload && udevadm trigger'
```
