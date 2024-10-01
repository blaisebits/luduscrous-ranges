Quickly deploy attack testing lab for use with HTB or THM

# Setup
1. Clone the repo
2. Add `xbufu.kali` ansible role
  1. `ludus ansible role add xbufu.kali`
3. customize config
  1. Add or remove any kali repo packages from the config file 
4. import the range config
  1. `ludus range config set -f HTBAttackLab/range-config.yaml`
5. build lab

# Usage
This range is designed to create a Kali and Windows testing machines for online CTFs such as HackTheBox and TryHackMe. This range uses approximately 34GB of RAM.

Simply copy your OpenVPN config to Kali to connect to the CTF.

SSH from Windows to the kali attack box with the `-D` option to setup a SOCKS5 Proxy

Example:
`PS> ssh kali@10.5.10.99 -D9050`

Lastly, configure Proxifier on Windows to proxy traffic to `127.0.0.1:9050`
