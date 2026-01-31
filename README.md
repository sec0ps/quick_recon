# Quick Recon

Quick Recon is an automated reconnaissance framework designed for penetration testers and security professionals who need to rapidly gather intelligence on target organizations during the early stages of an engagement.

## What It Does

The tool automates the tedious, repetitive tasks that eat up the first hours of any assessment. Point it at a target domain and client name, and it handles the rest - DNS enumeration, WHOIS lookups, certificate transparency log searches, email harvesting, LinkedIn employee discovery, GitHub secret scanning, and more. Results are compiled into structured output ready for analysis or reporting.

## Why It Exists

This started as a quick script to speed up initial recon. It grew. Rather than running a dozen different tools and manually correlating output, Quick Recon chains them together intelligently, validates findings, and eliminates the noise that wastes your time.

## Installation
```bash
git clone https://github.com/sec0ps/quick_recon.git && sudo apt install python3-requests python3-urllib3 python3-dnspython
cd quick_recon
```

## Usage
```bash
python quick_recon.py -d target.com -c "Target Company Name"
```

On first run, you'll be prompted to configure API keys for enhanced functionality (GitHub, Shodan, HIBP, LinkedIn). These are saved locally and validated automatically on subsequent runs.

## Key Features

- **DNS & Infrastructure** - Subdomain enumeration, WHOIS, ASN discovery, certificate transparency
- **Email Harvesting** - Web crawling, search engine dorking, PGP keyservers, theHarvester integration
- **LinkedIn Intelligence** - Company identification, employee enumeration with accurate filtering
- **GitHub Scanning** - Searches for leaked credentials, API keys, and secrets in public repos
- **Checkpoint/Resume** - Long-running scans can be interrupted and resumed

## Configuration

API keys and credentials are stored in `quick_recon_config.json` in the working directory. The tool validates credentials before use and prompts for replacement if they've expired.

## Output

Results are written to a timestamped directory containing JSON data, discovered files, and a summary report suitable for inclusion in assessment documentation.

## Professional Services

### Red Cell Security, LLC
For enterprise deployments, custom integrations, or professional security assessments:

- **Email**: keith@redcellsecurity.org
- **Website**: www.redcellsecurity.org
- **Services**: Custom RF security solutions, threat hunting, defensive countermeasures

## Disclaimer

This software is provided "as-is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and non-infringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software.

## License & Copyright

**Author**: Keith Pachulski  
**Company**: Red Cell Security, LLC  
**Email**: keith@redcellsecurity.org  
**Website**: www.redcellsecurity.org  

© 2026 Keith Pachulski. All rights reserved.

**License**: MIT License - You are free to use, modify, and distribute this software in accordance with the terms of the license.

## Support Development

If you find this project valuable for your security operations:

[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://paypal.me/sec0ps)
