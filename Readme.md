# VMware VM Snapshot Script

## Overview

This project contains Python scripts to interact with VMware vCenter to:
- Retrieve virtual machines (VMs) using their IP addresses.
- Create snapshots of VMs.
- Generate an Excel report of VM snapshots.

The script connects to vCenter, fetches details of virtual machines, creates snapshots, and stores the snapshot details in an Excel report.

---

## File Structure

### 1. `config.cnf`
This is the configuration file that stores credentials for different environments. It includes:

```ini
[INT]
int_host = your_vcenter_host
user = your_username
pass = your_password

[QA]
qa_host = your_vcenter_host
user = your_username
pass = your_password

[PROD]
prod_host = your_vcenter_host
user = your_username
pass = your_password
```

Update this file with your vCenter credentials before running the scripts.

---

### 2. `config_parser.py`
This script is responsible for:
- Reading `config.cnf`.
- Extracting credentials for different environments.

#### Example Code:

```python
#!/usr/bin/python3

import configparser

config = configparser.RawConfigParser()
config.read('config.cnf')

try:
    int_host = config['INT']['int_host'].strip()
    int_user = config['INT']['user'].strip()
    int_pass = str(config['INT']['pass']).strip()
    qa_host = config['QA']['qa_host'].strip()
    qa_user = config['QA']['user'].strip()
    qa_pass = config['QA']['pass'].strip()
    prod_host = config['PROD']['prod_host'].strip()
    prod_user = config['PROD']['user'].strip()
    prod_pass = config['PROD']['pass'].strip()
except Exception as e:
    print(e)
    print("Please verify the config file to ensure correctness.")
```

---

### 3. `multiple_ips.py`
This script:
- Connects to vCenter using credentials from `config_parser.py`.
- Retrieves VMs by IP address.
- Creates a snapshot for each VM found.
- Saves VM snapshot details into an Excel file.

#### Features:
- Takes a list of IPs and finds the corresponding VMs.
- Validates whether the VMs exist before proceeding.
- Creates a snapshot with a user-defined name.
- Saves snapshot details in an Excel report.

---

## Prerequisites

Ensure you have the required Python packages installed:

```bash
pip install openpyxl pyVmomi pyvim
```

---

## Usage

### Running the Script

```bash
python3 multiple_ips.py --ip "192.168.1.100,192.168.1.101" --name "Backup_2025" --no-memory --quiesce
```

#### Arguments:
| Argument       | Description |
|---------------|------------|
| `--ip`        | Comma-separated list of IP addresses of VMs. |
| `--name`      | Name of the snapshot (minimum 7 characters). |
| `--no-memory` | (Optional) Disables memory in the snapshot. |
| `--quiesce`   | (Optional) Enables file system quiescing before taking the snapshot. |

---

### Example:

```bash
python3 multiple_ips.py --ip "192.168.1.100,192.168.1.101" --name "DailyBackup"
```

- This will create snapshots named `DailyBackup` for the specified VMs.

---

## Output

- The script generates an Excel file:  
  **`vm_snapshots_<timestamp>.xlsx`**  
  containing the following details:

| VM Name  | VM IP  | Number of Snapshots | Snapshot Name | Description | Created On |
|----------|--------|---------------------|---------------|-------------|------------|
| VM1      | 192.168.1.100 | 2 | DailyBackup | Snapshot by Python script | 2025-02-12 14:30:00 |
| VM2      | 192.168.1.101 | 1 | DailyBackup | Snapshot by Python script | 2025-02-12 14:30:00 |

---

## Error Handling

- If a VM IP is not found, the script **exits with an error**.
- If a snapshot creation fails, the script will **continue with the remaining VMs**.
- If there is an issue with vCenter credentials, the script will display an **authentication error**.

---

## Notes

- **Ensure the vCenter credentials in `config.cnf` are correct.**
- The script requires **network access** to the vCenter server.
- The `--name` argument must be at least **7 characters** long.
- Run the script with **appropriate permissions** if needed.
- I have aaded a sample Jenkinsfile in the repo too. 
---

## License

This project is **open-source** and free to use.

---

## Contact

For issues, suggestions, or improvements, feel free to open an issue or reach out.

---

### Happy scripting! 🚀

