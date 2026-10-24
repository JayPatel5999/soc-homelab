# Publishing checklist

## Before the first commit: remove anything private

- Real public IP address, ISP name, or router login pages
- Tailscale auth keys, API tokens, and tailnet name
- Passwords, even lab ones, and any `.env` or key files
- Wi-Fi names and passwords
- MAC addresses and serial numbers in screenshots
- Screenshots showing browser tabs, email, or account names

Use the lab's private 10.10.x.x ranges freely. They are not sensitive.

## Put the repo on GitHub

1. Create an empty repository named `soc-homelab` on GitHub. Leave the README option unchecked.
2. In the project folder run:

```
git init
git add .
git commit -m "Initial homelab design and documentation"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/soc-homelab.git
git push -u origin main
```

3. In repository settings, add a short description and topics such as `homelab`, `proxmox`, `opnsense`, `wazuh`, `soc`, `blue-team`, `active-directory`.
4. Pin the repository on your GitHub profile.
5. Replace `YOUR-USERNAME` in `website/index.html` with your GitHub username.

## Keep it honest

- Leave scenarios as "Planned" until you have run them and have real screenshots.
- Update [build-log.md](build-log.md) as each step is finished.
- Interviewers read commit history. Small, regular commits look better than one large upload.

## Add evidence as you go

Create `docs/img/` and add:

- Proxmox summary page
- OPNsense interface and firewall rules pages
- Wazuh dashboard with agents connected
- One screenshot per detection scenario showing the alert

Then reference them from the matching detection write-up.

## Put it on your portfolio website

`website/index.html` is a single self-contained page with the diagrams embedded. Two ways to use it:

1. Upload it as its own page, for example `yoursite.com/homelab`. Rename it to `index.html` inside a `homelab` folder if your host serves folders.
2. Copy the `<main>` section into an existing page template and keep the `<style>` block.

For free hosting, GitHub Pages works: repository settings, Pages, deploy from the `main` branch, folder `/website`.

## Resume line to use once it is true

Built and documented a segmented SOC homelab on Proxmox with OPNsense, Active Directory, and Wazuh, with attack-to-detection write-ups mapped to MITRE ATT&CK.

Only use this after at least one scenario is complete.
