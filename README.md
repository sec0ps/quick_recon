# Quick Recon

Quick Recon is an automated reconnaissance framework designed for penetration testers and security professionals who need to rapidly gather intelligence on target organizations during the early stages of an engagement.

## What It Does

The tool automates the tedious, repetitive tasks that eat up the first hours of any assessment. Point it at a target domain and client name, and it handles the rest - DNS enumeration, WHOIS lookups, certificate transparency log searches, email harvesting, LinkedIn employee discovery, GitHub secret scanning, M365/Azure AD tenant attribution, ADFS endpoint discovery, remote access appliance fingerprinting, and more. Results are compiled into structured output ready for analysis or reporting.

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

### Common Run Modes

```bash
# Full reconnaissance with IP ranges
python quick_recon.py -d target.com -i 192.168.1.0/24 -c "Target Corp"

# OSINT-only mode (no IP ranges provided)
python quick_recon.py -d target.com -c "Target Corp"

# Resume an interrupted scan
python quick_recon.py -d target.com -c "Target Corp" --resume

# Run a single module only
python quick_recon.py -d target.com -c "Target Corp" --m365-only
python quick_recon.py -d target.com -c "Target Corp" --linkedin-only --linkedin-mode paranoid
python quick_recon.py -d target.com -c "Target Corp" --email-only --deep-crawl
```

## Key Features

### Core Reconnaissance
- **DNS & Infrastructure** - Subdomain enumeration via certificate transparency logs (crt.sh, certspotter, hackertarget), DNS bruteforce, WHOIS correlation, ASN discovery via Team Cymru and RIPE
- **Subdomain Takeover Detection** - Validated detection across 10+ cloud services with confidence scoring (HIGH/MEDIUM/LOW)
- **Technology Stack Identification** - Server fingerprinting, framework detection, security header analysis across default and alternate HTTP ports (80, 443, 8000, 8080, 8443, 8888)
- **Remote Access Appliance Fingerprinting** - Identifies VPN/remote access appliances exposed externally including F5 BIG-IP, Citrix Netscaler/ADC, FortiGate SSLVPN, GlobalProtect, Cisco AnyConnect/ASA, Pulse Secure/Ivanti, SonicWall, Microsoft RD Web Access, and Check Point Mobile Access with version extraction

### Cloud Identity Reconnaissance
- **M365/Azure AD Tenant Attribution** - Unauthenticated tenant discovery via openid-configuration and userrealm endpoints, returning tenant ID, region, brand name, and federation posture (Managed vs Federated)
- **ADFS Endpoint Discovery** - Automatic ADFS reconnaissance for federated tenants including version banner extraction, federation metadata parsing, supported protocols (SAML2, WS-Federation, WS-Trust, OAuth2), token signing certificate details, and ADFS version inference from build numbers

### Cloud Storage Enumeration
- **AWS S3 Bucket Discovery** - Multi-region bucket enumeration with sensitivity-based content download
- **Azure Blob Storage Discovery** - Storage account enumeration across common container names
- **GCP Cloud Storage Discovery** - Bucket enumeration with public/private classification

### OSINT & Credential Intelligence
- **Email Harvesting** - High-yield prioritized web crawling, sitemap-aware URL discovery, search engine dorking (Google, Bing, DuckDuckGo, Yahoo), PGP keyservers, theHarvester integration, and email pattern detection
- **LinkedIn Intelligence** - Company identification, employee enumeration with company ID filtering, human-like delay modes (fast/normal/paranoid) to avoid rate limiting and account lockouts
- **GitHub Secret Scanning** - Searches for leaked credentials, API keys, JWT tokens, AWS keys, and secrets in public repositories, gists, and issues with automatic download of files containing detected secrets
- **Breach Database Lookups** - HIBP API integration for compromised credential discovery

### Operational Features
- **Checkpoint/Resume** - Long-running scans can be interrupted and resumed from the last checkpoint
- **State Management** - Automatic state persistence with module-level status tracking and interruption recovery
- **Single-Module Execution** - Run any individual module via `--X-only` flags for targeted reconnaissance
- **Granular Skip Controls** - Skip specific modules via `--skip-X` flags for engagement-specific scoping
- **Responsive Interrupt Handling** - Graceful Ctrl-C handling with state preservation

## Command-Line Reference

### Skip Flags
- `--skip-m365` - Skip M365/Azure AD tenant attribution
- `--skip-adfs` - Skip ADFS endpoint discovery
- `--skip-breach-check` - Skip breach database checking
- `--skip-scan` - Skip network scanning
- `--skip-s3` - Skip S3 bucket enumeration
- `--skip-azure` - Skip Azure storage enumeration
- `--skip-gcp` - Skip GCP storage enumeration
- `--skip-github` - Skip GitHub secret scanning
- `--skip-asn` - Skip ASN enumeration
- `--skip-subdomain-takeover` - Skip subdomain takeover detection
- `--skip-osint` - Skip all OSINT modules

### Single-Module Execution Flags
- `--m365-only` - M365 tenant attribution only
- `--adfs-only` - ADFS endpoint discovery (runs M365 attribution first)
- `--linkedin-only` - LinkedIn enumeration only
- `--github-only` - GitHub secret scanning only
- `--s3-only` - S3 bucket enumeration only
- `--azure-only` - Azure storage enumeration only
- `--gcp-only` - GCP storage enumeration only
- `--asn-only` - ASN enumeration only
- `--subdomain-takeover-only` - Subdomain takeover detection only
- `--email-only` - Email harvesting only
- `--dns-only` - DNS enumeration only
- `--breach-only` - Breach database check only
- `--techstack-only` - Technology stack identification only

### Behavioral Flags
- `--linkedin-mode {fast,normal,paranoid}` - LinkedIn delay profile (default: normal). Use paranoid for sensitive engagements.
- `--linkedin-max-results N` - Maximum LinkedIn employee results to fetch (default: 100)
- `--deep-crawl` - Enable deep email crawl mode (100 pages, depth 3) - slower but more thorough
- `--resume` - Automatically resume from last checkpoint without prompting

## Configuration

API keys and credentials are stored in `quick_recon_config.json` in the working directory. The tool validates credentials before use and prompts for replacement if they've expired.

Supported integrations:
- GitHub Personal Access Token (secret scanning)
- LinkedIn session cookies (employee enumeration)
- HIBP API key (breach database queries)
- Shodan API key (planned integration)
- Censys API credentials (planned integration)

## Output

Results are written to a timestamped directory containing:
- **JSON results file** - Complete structured data from all modules
- **Markdown report** - Human-readable summary with section-organized findings
- **Report template** - Narrative content suitable for inclusion in assessment deliverables
- **State file** - Resume checkpoint data (preserved until scan completes successfully)
- **Downloaded artifacts** - Files extracted from cloud storage, GitHub repositories, etc.

## Recent Changes

### Cloud Identity & Federation
- Added M365/Azure AD tenant attribution module covering tenant ID, region, brand name, and federation posture
- Added ADFS endpoint discovery for federated tenants with version banner extraction, federation metadata parsing, and protocol support enumeration
- Both modules integrate with existing scope validation and run automatically as part of the standard flow

### Remote Access Appliance Detection
- Extended technology stack identification with VPN and remote access appliance fingerprinting
- Probes default ports (80, 443) and common alternate ports (8000, 8080, 8443, 8888) for service detection
- Conditional secondary path probing only fires on ambiguous responses to limit traffic volume
- Captures appliance class, version, and detection evidence per host

### LinkedIn Rate Limit Hardening
- Replaced fixed 5-second delays with multi-layer human-behavior delay model including per-page jitter, occasional long-read pauses, and between-company breaks
- Added three delay profiles: `fast` (testing), `normal` (default, human-like timing), `paranoid` (sensitive engagements)
- Hard session cap to prevent runaway API consumption
- Active rate-limit detection (HTTP 429, 999, challenge redirects, consecutive empty results)
- Clean exit with partial results preserved on rate-limit triggers

### Email Harvesting Optimization
- Rewrote web crawling with high-yield URL prioritization (Contact, About, Team, Staff, Leadership pages crawled first)
- Sitemap fast-path eliminates link-discovery overhead on sites that publish sitemap.xml
- Early termination after 8 consecutive pages with no new emails
- Reduced default page cap from 100 to 25 with depth from 3 to 2
- Default email harvesting runtime reduced from worst-case ~14 minutes to typical 30-90 seconds
- `--deep-crawl` flag restores the previous aggressive crawl behavior when needed

### Interrupt Responsiveness
- Added shutdown flag propagation to long-running loops
- Ctrl-C now responds within seconds rather than waiting for in-flight HTTP requests to complete
- State save happens immediately on interrupt with partial results preserved

### Reporting
- Markdown report and report template expanded to include M365 tenant attribution, ADFS reconnaissance, remote access appliance findings, and alternate-port services
- Federated vs Managed tenant context drives different narrative content in the report template
- Internal DNS information disclosure surfaces as a separate finding category

## Professional Services

### Red Cell Security, LLC

For enterprise deployments, custom integrations, or professional security assessments:

- **Email**: keith@redcellsecurity.org
- **Website**: [www.redcellsecurity.org](https://www.redcellsecurity.org)
- **Services**: Penetration testing, vulnerability assessments, threat hunting, defensive countermeasures, custom security tooling development

## Disclaimer

This software is provided "as-is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and non-infringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software.

This tool is intended for authorized security testing only. Users are responsible for ensuring compliance with all applicable laws and regulations. Unauthorized use of these tools may violate local, state, federal, and international laws.

## License & Copyright

**Author**: Keith Pachulski
**Company**: Red Cell Security, LLC
**Email**: keith@redcellsecurity.org
**Website**: [www.redcellsecurity.org](https://www.redcellsecurity.org)
© 2026 Keith Pachulski. All rights reserved.

**License**: MIT License - You are free to use, modify, and distribute this software in accordance with the terms of the license.

## Support Development

If you find this project valuable for your security operations:

[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://paypal.me/sec0ps)
