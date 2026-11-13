# Tongdiyabendalnancwat-PENETRATION-TESTING-REPORT-NETWORKWALKS
# Week 2 | Project Module 1: Footprinting, Reconnaissance and Network Scanning

Passive reconnaissance of **networkwalks.com**, the lab target provided by the Networkwalks cybersecurity and ethical hacking course, using built-in Kali Linux tools. The repository also covers a **Zenmap** scan of the local lab network (Appendix A of the report).

**Prepared by:** Tongdiya Bendal Nancwat (Coder Tech)
**Testing dates:** 9-11 November 2026
**Report:** [Networkwalks_Recon_Report.html](Networkwalks_Recon_Report.html)

> For educational purposes only. Everything here was done against the lab target named in the course material. Testing systems without the owner's written permission is illegal.


## Tools and commands

| # | Tool | Command | Purpose |
|---|------|---------|---------|
| 1 | whois | `whois networkwalks.com` | Registrar, dates, name servers |
| 2 | whatweb | `whatweb networkwalks.com` | Web server, CMS, plugin and library versions |
| 3 | nslookup | `nslookup networkwalks.com` | Resolve the domain to its IP address |
| 4 | curl | `curl -I https://networkwalks.com` | Read HTTP response headers |
| 5 | wafw00f | `wafw00f networkwalks.com` | Detect a web application firewall |
| 6 | dnsrecon | `dnsrecon -d networkwalks.com` | Enumerate DNS, mail, SPF and SRV records |

## Key findings

| Area | Finding |
|------|---------|
| Registrar | GoDaddy.com, LLC. Created 2019-11-06, expires 2027-11-06 |
| Hosting | HostGator name servers (ns6135 and ns6136.hostgator.com) |
| Server IP | 192.232.216.135 (United States) |
| Web stack | Apache with nginx caching, WordPress 7.1.4, WordPress Download Manager 3.3.76, jQuery 3.7.1 |
| HTTP | HTTP redirects to HTTPS. Cookie is Secure and HttpOnly. WordPress REST API advertised in headers |
| Firewall | ModSecurity (SpiderLabs) WAF detected |
| Mail | mail.networkwalks.com on the same IP as the website. SPF ends in `~all` (soft fail) |
| DNS software | BIND 9.16.23-RH visible on both name servers |
| DNSSEC | Not signed |
| Exposed contact | info@networkwalks.com |

## Main recommendations

1. Keep WordPress core and every plugin up to date, and remove unused plugins.
2. Add the security headers that are missing (HSTS, CSP, X-Frame-Options, X-Content-Type-Options).
3. Restrict anonymous access to the WordPress REST API user listing.
4. Hide version strings and replace the public email address with a contact form.
5. Move SPF to `-all` once all senders are listed, add DKIM and DMARC, and enable DNSSEC if supported.

## Network scanning with Zenmap

The second part of the work scans the local lab network with **Zenmap**, the graphical front end for Nmap, and maps what it finds in the Topology view. This is separate from the networkwalks.com footprinting and only covers my own local network.

![Zenmap topology map](screenshots/07_topology_map.png)

| Item | Result |
|------|--------|
| Scanner | `localhost` (black node in the centre) |
| Host found | `192.168.56.1` (green node), one hop from the scanner |
| Open ports | Green in Zenmap means fewer than 3 open ports |
| Network range | `192.168.56.x` is the default VirtualBox host-only range, so `.1` is most likely the host computer |

The topology view shows which hosts replied and roughly how many ports are open. It does not list port numbers, service versions or OS details, so none are claimed here.

<!-- TODO: add the exact Nmap command or Zenmap profile used, and the Ports/Hosts output -->

Only scan networks you own or have written permission to test.

## Notes on the lab setup

Several first attempts failed because the Kali VM's default resolver (10.0.0.1) refused DNS queries. These were local problems, not findings about the target. Setting a public resolver fixed them:

```bash
sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
dnsrecon -d networkwalks.com --lifetime 10 | tee dnsrecon_output.txt
```

## Disclaimer

This work is for learning only. The author is not responsible for misuse. Hacking is legal only on systems you own, in a lab environment, or under a signed agreement with an agreed scope.
