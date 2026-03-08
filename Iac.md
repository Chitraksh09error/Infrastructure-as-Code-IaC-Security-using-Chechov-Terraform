## Install Checkov
```bash
sudo apt update
```
## (DON'T USE BELOW CODE)
```bash
sudo apt install python3-pip -y
pip3 install checkov
```
The above command is an error in an externally managed environment because Ubuntu (Python 3.12) now protects the system Python environment using PEP 668. It prevents pip from installing packages globally to avoid breaking OS packages.
