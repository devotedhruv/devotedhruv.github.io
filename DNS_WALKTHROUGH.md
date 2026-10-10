# DNS Walkthrough: How the Internet Finds Your Website

**Author:** Dhurbaraj Singh  
**Course:** General AI Fluency (Track PF-04, Week 5)  
**Deliverable Document:** DNS Infrastructure & Technical Architecture Walkthrough  

---

## 1. What DNS Does: The Internet's Universal GPS

Computers communicating across the global Internet do not understand human-friendly words like `devotedhruv.github.io`, `google.com`, or `netflix.com`. Underneath the surface, every computer, server, and load balancer is identified by a numerical **IP (Internet Protocol) address** (like `185.199.108.153` for IPv4, or `2606:4700:3033::6815:150d` for IPv6).

If humans had to memorize twelve-digit numerical strings for every website, the web would be unusable. 

**The Domain Name System (DNS)** is the global, decentralized address book of the Internet. Its sole fundamental job is **translation**: taking a human-readable domain name that you type into a browser and translating it into the exact numerical IP address of the server hosting the site's files.

---

## 2. What Happens Between Typing an Address and Seeing the Page

When a user types `https://devotedhruv.github.io` into their browser and presses Enter, an automated relay race occurs behind the scenes in under 50 milliseconds. Here is the four-step journey, explained simply:

```
[ User Browser ]
       │
       ▼ (1) "Where is devotedhruv.github.io?"
[ Recursive Resolver (ISP / 1.1.1.1) ]
       │
       ├─► (2) Asks Root Nameserver (.) ───────────► "Go ask the .io TLD server"
       ├─► (3) Asks .io TLD Nameserver ────────────► "Go ask GitHub's Authoritative server"
       └─► (4) Asks Authoritative Nameserver ──────► "Here is the record: 185.199.108.153"
       │
       ▼ (5) Returns IP to Browser
[ Browser connects to 185.199.108.153 via HTTPS ] ──► [ Page loads! ]
```

### Step 1: The Detective (The Recursive Resolver)
First, your browser checks its own local memory (cache). If you haven't visited the site recently, your computer asks a **Recursive Resolver** (typically operated by your Internet Service Provider or public resolvers like Cloudflare `1.1.1.1` or Google `8.8.8.8`). Think of the resolver as a persistent librarian or detective whose job is to track down the answer on your behalf.

### Step 2: The Direction Post (Root Nameservers)
The resolver doesn't know the answer yet, so it asks a **Root Nameserver** (represented by the invisible `.` at the very end of every URL). There are 13 named root server clusters globally. The root server says:
> *"I don't know the exact website, but I know who manages the `.io` top-level domain. Go ask the `.io` TLD nameserver."*

### Step 3: The Registry (TLD Nameservers)
The resolver travels to the **Top-Level Domain (TLD) Nameserver** for `.io`. The TLD server inspects its registry and replies:
> *"I know who manages `github.io`! Go speak with GitHub's Authoritative Nameservers at `ns1.github.com`."*

### Step 4: The Official Record Keeper (The Authoritative Nameserver)
Finally, the resolver arrives at the **Authoritative Nameserver**. This server holds the true, binding DNS zone records configured for `github.io`. The authoritative nameserver looks up `devotedhruv` and answers:
> *"Found it! The website files are served at IP address `185.199.108.153`."*

The resolver sends this IP back to your browser, and saves it in its local cache with a **TTL (Time To Live)** timer so it doesn't have to repeat the entire journey on the next page reload.

---

## 3. What is a CNAME Record? (And Why Do Hosts Use It?)

In DNS management, you will encounter two primary record types:

1. **A Record (Address Record):**  
   Directly maps a domain name to a static, fixed IP address.  
   *Example:* `mywebsite.com` $\rightarrow$ `192.0.2.1`

2. **CNAME Record (Canonical Name):**  
   Maps a domain name to **another domain name (an alias)** rather than a raw IP address.  
   *Example:* `www.dhurbaraj.com` $\rightarrow$ `devotedhruv.github.io` (or `dhurbaraj.netlify.app`)

### Why Cloud Hosts (Netlify, GitHub Pages, Vercel) Rely on CNAME Records:
In modern web hosting, companies like GitHub or Netlify do not run a single physical server in a basement. They operate global Content Delivery Networks (CDNs) with hundreds of edge servers distributed across North America, Europe, Asia, and South America. Their internal routing, IP addresses, and load balancers change frequently to optimize routing and mitigate DDoS attacks.

If you pointed your personal domain to a raw static IP address (via an A Record), and GitHub moved their cluster, your website would instantly go down. 

By using a **CNAME record**, you simply tell the world: *"Go wherever `devotedhruv.github.io` points."* If GitHub updates its routing infrastructure tomorrow, your custom domain updates automatically without you ever having to touch your DNS settings again.

---

## 4. How HTTPS and SSL Certificates Work

When your browser receives the IP address `185.199.108.153`, it initiates a **TCP 3-way handshake** followed by a **TLS/SSL cryptographic handshake**:
1. The host server presents a cryptographic **SSL Certificate** issued by an accredited Certificate Authority (such as Let's Encrypt or DigiCert).
2. The browser validates that the certificate is currently active, unexpired, and cryptographically signed for `*.github.io` (or your custom domain).
3. Once verified, the browser displays the secure green padlock in the URL bar, and all subsequent traffic (HTML, CSS, resume downloads) is encrypted using AES-GCM encryption so no intermediary on the network can eavesdrop or tamper with the data.

---

## 5. Explaining Every File in My Deployed Site

My deployed website at **`https://devotedhruv.github.io`** consists of clean, hand-audited files:

1. **`index.html`**:
   - The primary entrypoint document.
   - Contains semantic HTML5 tags (`<header>`, `<main>`, `<section>`, `<footer>`) for accessibility.
   - Uses CSS design tokens (custom variables for colors and typography) with responsive media queries that adapt cleanly to mobile, tablet, and widescreen displays.
   - Contains OpenGraph meta tags so that when the URL is shared on LinkedIn or Twitter, it renders a rich card preview.
   - Embeds working, verified links to my **LinkedIn**, **GitHub**, **Booking Link (Cal.com)**, and **CV**.
   - Features a dedicated **Research & Capstone Space** with a placeholder reserved for the official FlyRank completion badge upon graduation.
2. **`Dhurbaraj_Singh_CV.pdf`**:
   - The authentic, static resume asset served directly over HTTPS so recruiters and team leads can inspect my experience in one click.
3. **`README.md`**:
   - Repository documentation outlining build and deployment status.
4. **`DNS_WALKTHROUGH.md`**:
   - This architectural walkthrough explaining DNS resolvers, nameservers, CNAME aliases, and HTTP/TLS delivery.

---

## 6. Verification Checklist
- [x] Deployed live over HTTPS on public free host (`https://devotedhruv.github.io`).
- [x] Tested in logged-out private browser window: loads in <200ms with valid SSL certificate.
- [x] Working links verified: LinkedIn, GitHub, CV download, booking link, and email.
- [x] DNS walkthrough written in plain English, explaining resolvers, roots, TLDs, authoritative servers, and CNAME records.
