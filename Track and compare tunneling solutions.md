# Track and compare tunneling solutions
[awesome-tunneling/README.md at master · anderspitman/awesome-tunneling](https://github.com/anderspitman/awesome-tunneling/blob/master/README.md) 

 The purpose of this list is to track and compare tunneling solutions. This is primarily targeted toward self-hosters and developers who want to do things like exposing a local webserver via a public domain name, with automatic HTTPS, even if behind a NAT or other restricted network.

**NOTE:** We're building a community around self-hosting, data ownership, and decentralization in general. Join us over at [IndieBits.io](https://forum.indiebits.io/).

I started this list because I'm looking for a simple tool/service that does the following:

*   Allows me to register a domain name and automatically points the records at the server running the tunnels.
*   Automatically sets up and manages HTTPS certificates (apex and subdomains) for the domain.
*   Provides a client tool that tunnels HTTP/TCP connections through the server without requiring root on the client.
*   Provides a simple GUI interface to allow me to map X domain/subdomain to Y port on Z client, and proxy all connections to that domain.

So far I haven't found a tool that does all of this. In particular, while some of them can do automatic certs through Let's Encrypt, none of them integrate the domain registration and DNS management in a simple way.

*   For most people, I currently recommend [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/). Although it's closed source, this is the production-quality service that gets the closest to achieving the dream. It's also a loss-leader for Cloudflare's other products which means they can offer it for free.
*   If you want to self-host, there are many options. For something production ready [frp](https://github.com/fatedier/frp) is probably what you want. If you're a developer, I'd recommend starting with my own [SirTunnel](https://github.com/anderspitman/SirTunnel) project and modifying it for your needs. For non-developers and those wanting more of a GUI experience, I created [boringproxy](https://boringproxy.io/). It's my take on a comprehensive tunnel proxy solution. It's in beta but currently solves almost everything I want. Once the server is running this is a very easy tool to use and has some nice features.

*   [Telebit](https://telebit.cloud/) - Written in JS. [Code](https://git.coolaj86.com/coolaj86/telebit.js).
*   [tunnel.pyjam.as](https://tunnel.pyjam.as/) - No custom client; uses WireGuard directly instead. Written in Python. [source code](https://gitlab.com/pyjam.as/tunnel)
*   [SSH-J.com](https://bitbucket.org/ValdikSS/dropbear-sshj/) - Public SSH Jump & Port Forwarding server. No software, no registration, just an anonymous SSH server for forwarding. Users are encouraged to use it for SSH exposure only, to preserve end-to-end encryption. No public ports, only in-SSH connectivity. Run `ssh ssh-j.com` and it will display usage information.
*   [frp](https://github.com/fatedier/frp) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c951cb0e-1e3b-46f5-8e6a-04593a7337d5.svg%2Bxml?raw=true)
    ](https://github.com/fatedier/frp/stargazers) - Comprehensive open alternative to ngrok. Supports UDP, and has a P2P mode. Supports multiplexing over TCP (single connection or pool), QUIC, and KCP.
*   [ngrok 1.0](https://github.com/inconshreveable/ngrok) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/5bc20c5c-4318-4dd0-a526-95837360f1f5.svg%2Bxml?raw=true)
    ](https://github.com/inconshreveable/ngrok/stargazers) - Original version of ngrok. No longer developed in favor of the commercial 2.0 version.
*   [localtunnel/localtunnel](https://github.com/localtunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/470d99bc-2089-4cf5-8634-a0fdc7e9d91c.svg%2Bxml?raw=true)
    ](https://github.com/localtunnel/localtunnel/stargazers) - Written in node. Popular suggestion.
*   [chisel](https://github.com/jpillora/chisel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/22d0237c-35cb-4d8b-8197-808a072deb96.svg%2Bxml?raw=true)
    ](https://github.com/jpillora/chisel/stargazers) - SSH under the hood, but still uses a custom client binary. Supports auto certs from LetsEncrypt. Written in Go.
*   [sshuttle](https://github.com/sshuttle/sshuttle) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/728f62ae-0f1b-4bcd-97c1-567c9d0fb898.svg%2Bxml?raw=true)
    ](https://github.com/sshuttle/sshuttle/stargazers) - Open source project originally from one of the founders of Tailscale. Server doesn't require root; client does. Explicitly designed to avoid TCP-over-TCP issues.
*   [rathole](https://github.com/rapiz1/rathole) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/194d35df-1b84-4d37-b4bf-da8c55611a03.svg%2Bxml?raw=true)
    ](https://github.com/rapiz1/rathole/stargazers) - Similar to frp, including the config format, but with improved performance. Low resource consumption. Hot reload. Written in Rust.
*   [bore](https://github.com/ekzhang/bore) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/03c59bcd-c5eb-4ce1-87a1-8da691199963.svg%2Bxml?raw=true)
    ](https://github.com/ekzhang/bore/stargazers) - Minimal tunneling solution. MIT Licensed. Written in Rust.
*   [Pangolin](https://github.com/fosrl/pangolin) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/59998725-829b-468b-91cd-762c7a7bbd9e.svg%2Bxml?raw=true)
    ](https://github.com/fosrl/pangolin) - Fully self-hostable tunneled reverse proxy management server with identity and access control, automated SSL certificates, and dashboard UI.
*   [expose](https://github.com/beyondcode/expose) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c9b1ad57-7345-4564-b980-7f340a4c5c6f.svg%2Bxml?raw=true)
    ](https://github.com/beyondcode/expose/stargazers) - ngrok alternative written in PHP.
*   [sish](https://github.com/antoniomika/sish) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/87badf00-cf38-435b-9543-277d70b5a4f9.svg%2Bxml?raw=true)
    ](https://github.com/antoniomika/sish/stargazers) - Open source ngrok/serveo alternative. SSH-based but uses a custom server written in Go. Supports WebSocket tunneling.
*   [wstunnel](https://github.com/erebe/wstunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/4610276c-457e-41da-8ba2-e5771137b23e.svg%2Bxml?raw=true)
    ](https://github.com/erebe/wstunnel/stargazers) - Proxies over WebSockets. Focus on proxying from behind networks that block certain protocols. Written in Rust with executables provided.
*   [gost](https://latest.gost.run/en/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/0601637f-f487-463a-91a8-5f2b85cf0f9d.svg%2Bxml?raw=true)
    ](https://github.com/go-gost/gost/stargazers) - Looks like a comprehensive option. TCP and UDP tunneling. TAP/TUN devices. Load balancing. Web API. Written in Go.
*   [progrium/localtunnel](https://github.com/progrium/localtunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/a27de0c7-fd6a-42fa-94a4-08c851d25394.svg%2Bxml?raw=true)
    ](https://github.com/progrium/localtunnel/stargazers) - As far as I know this is the first ever tool of this kind, predating ngrok and the other localtunnel. No longer maintained, but here for posterity. MIT License. Written in Go.
*   [go-http-tunnel](https://github.com/mmatczuk/go-http-tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/a8baf0a3-5f39-476f-b3d1-833942c51716.svg%2Bxml?raw=true)
    ](https://github.com/mmatczuk/go-http-tunnel/stargazers) - Uses a single HTTP/2 connection for muxing. Need to manually generate certs for server and clients.
*   [pgrok/pgrok](https://github.com/pgrok/pgrok) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c7814b74-6365-4d63-b4cb-cea93598a8a3.svg%2Bxml?raw=true)
    ](https://github.com/pgrok/pgrok/stargazers) - A multi-tenant HTTP reverse tunnel solution through SSH remote port forwarding.
*   [zrok](https://zrok.io/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/6e45887f-285b-4753-a570-dcfe7f5e9b02.svg%2Bxml?raw=true)
    ](https://github.com/openziti/zrok/stargazers) - Aims for effortless sharing both publicly and privately. Supports multiple types of resources, including HTTP endpoints and files. Built on OpenZiti (see overlay section below). Apache 2 License. Written in Go.
*   [portr](https://portr.dev/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/4cb4c112-52a2-44b2-939d-3de415bfb96d.svg%2Bxml?raw=true)
    ](https://github.com/amalshaji/portr/stargazers) - Has a JavaScript/Python admin page and request inspection/replay features. AGPL-3.0 License. Tunneling implemented in Go.
*   [tunnelto](https://tunnelto.dev/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/14270dfe-57ff-4384-9328-71833486ff3a.svg%2Bxml?raw=true)
    ](https://github.com/agrinman/tunnelto/stargazers) - Open source (MIT). Written in Rust.
*   [piko](https://github.com/andydunstall/piko) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/8feffa34-b108-4142-a7d4-acff2b219caf.svg%2Bxml?raw=true)
    ](https://github.com/andydunstall/piko/stargazers) - Piko is an open-source alternative to Ngrok, designed to serve production traffic and be simple to host (particularly on Kubernetes). MIT License. Written in Go.
*   [gsocket/Global Socket](https://www.gsocket.io/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/d80c5ed2-3baf-44ee-8050-f6ba4a40b4be.svg%2Bxml?raw=true)
    ](https://github.com/hackerschoice/gsocket/stargazers) - The Global Socket Toolkit allows two users behind NAT/Firewall to establish a TCP connection with each other. Securely. Written in C.
*   [SirTunnel](https://github.com/anderspitman/SirTunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/0ac2e1b1-9124-41a1-8020-f80de75b3fea.svg%2Bxml?raw=true)
    ](https://github.com/anderspitman/SirTunnel/stargazers) - Minimal, self-hosted, 0-config alternative to ngrok. Similar to sish but leverages Caddy+OpenSSH rather than custom server code.
*   [boringproxy](https://boringproxy.io/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/5e1def59-f143-4d5e-9c9f-fb3211f5a20b.svg%2Bxml?raw=true)
    ](https://github.com/boringproxy/boringproxy/stargazers) - Designed to be very easy to use. No config files. Clients can be remote-controlled through a simple WebUI and/or REST API on the server.
*   [Tunnelmole](https://github.com/robbie-cahill/tunnelmole-client/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/fa0e3194-aaf2-46d2-91a5-d9e2968c26c6.svg%2Bxml?raw=true)
    ](https://github.com/robbie-cahill/tunnelmole-client/stargazers) - Open source and optionally self hostable. The client and server are both written in TypeScript.
*   [jprq](https://github.com/azimjohn/jprq) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/0399b7c5-5e91-4aff-bc1e-ceab7e2ba35d.svg%2Bxml?raw=true)
    ](https://github.com/azimjohn/jprq/stargazers) - Proxies over WebSockets. Written in Go.
*   [Wiretap](https://github.com/sandialabs/wiretap) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/7c238dbf-b786-46dc-85cd-be29bc883e3b.svg%2Bxml?raw=true)
    ](https://github.com/sandialabs/wiretap/stargazers) - Transparent tunneling over WireGuard (UDP) using userspace network stack. Root not required on server. Supports multiple clients and servers. Written in Go.
*   [PageKite](https://pagekite.net/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/dac12021-6d00-4173-b2e1-8ec02db6bea9.svg%2Bxml?raw=true)
    ](https://github.com/pagekite/PyPagekite/stargazers) - Comprehensive open source solution with hosted options.
*   [onionpipe](https://github.com/cmars/onionpipe) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/1f6ed140-13f4-4911-9ad0-b6b7939b320f.svg%2Bxml?raw=true)
    ](https://github.com/cmars/onionpipe/stargazers) - Onion addresses for anything. `onionpipe` forwards ports on the local host to remote Onion addresses as Tor hidden services and vice-versa. Written in Go.
*   [Crowbar](https://github.com/q3k/crowbar) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/190d1724-f3be-4526-9853-421630309ee5.svg%2Bxml?raw=true)
    ](https://github.com/q3k/crowbar/stargazers) - Tunnels TCP connections over HTTP GET and POST requests.
*   [tunneller](https://github.com/skx/tunneller) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/3a157762-e05d-47af-964b-addf9b40b716.svg%2Bxml?raw=true)
    ](https://github.com/skx/tunneller/stargazers) - Open source. Written in Go.
*   [tunnel](https://github.com/koding/tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/1e344bac-5a6d-486c-b969-5f563e9ee662.svg%2Bxml?raw=true)
    ](https://github.com/koding/tunnel/stargazers) - This one is a Golang library, not a program you can just run. However, it looks easy to use for creating custom solutions. Uses a single TCP socket, and [yamux](https://github.com/hashicorp/yamux) for multiplexing.
*   [jerson/pgrok](https://www.proxy.jetzt/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/585293c0-6a30-4815-ab1c-bc27fc697184.svg%2Bxml?raw=true)
    ](https://github.com/jerson/pgrok/stargazers) - Fork of ngrok 1.0, with more recent commits. Archived.
*   [remotemoe](https://github.com/fasmide/remotemoe) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/54b2bd77-ed4f-4502-a855-fbc4d3da0064.svg%2Bxml?raw=true)
    ](https://github.com/fasmide/remotemoe/stargazers) - SSH-based, with custom golang server. Does some cool unique things. Instead of just plain tunnels, it drops you into a basic CLI UI that offers several useful commands interactively, such as adding a custom hostname. Also allows end-to-end encryption for both HTTPS and upstream SSH. Doesn't appear to offer non-e2e HTTPS, ie no auto Let's Encrypt support.
*   [docker-tunnel](https://github.com/vitobotta/docker-tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/6fc848fb-e761-406d-b501-a8fd7481630a.svg%2Bxml?raw=true)
    ](https://github.com/vitobotta/docker-tunnel/stargazers) - Simple Docker-based nginx+SSH solution.
*   [hypertunnel](https://github.com/berstend/hypertunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/e7d56f24-2958-4835-bc5d-c710c5317b0d.svg%2Bxml?raw=true)
    ](https://github.com//berstend/hypertunnel/stargazers) - Public server appears to be down. MIT Licensed. Written in JavaScript.
*   [tunwg](https://github.com/ntnj/tunwg) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/02947e9a-4974-4ca1-9e8a-f10a59978c4c.svg%2Bxml?raw=true)
    ](https://github.com/ntnj/tunwg/stargazers) - Wireguard in userspace based. Offers end to end encrypted TLS with LetsEncrypt certificates generated automatically by clients, with support for custom domains. Server can be self-hosted and doesn't require storing any data.
*   [reverse-tunnel](https://github.com/snsinfu/reverse-tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c9942348-e7e3-487f-b1a2-edea4ca4108a.svg%2Bxml?raw=true)
    ](https://github.com/snsinfu/reverse-tunnel/stargazers) - Support TCP and UDP tunnels. Has docker images. Supports Let's Encrypt. MIT License. Written in Go.
*   [gt](https://github.com/ao-space/gt)[![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/3e40fad6-ea61-4b20-8bcc-6ae8df89edc1.svg%2Bxml?raw=true)
    ](https://github.com/ao-space/gt/stargazers) - Supports peer-to-peer direct connection (P2P) and Internet relay. Focus on performance. Written in Go.
*   [jkuri/bore](https://github.com/jkuri/bore) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/94439f6f-c376-463b-8fe3-ac67066eea49.svg%2Bxml?raw=true)
    ](https://github.com/jkuri/bore/stargazers) - Reverse HTTP/TCP proxy via SSH. Written in Go.
*   [EXPOSE](https://github.com/exposesh) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/5d090e83-6dac-4058-96ad-96f80f298de6.svg%2Bxml?raw=true)
    ](https://github.com/exposesh/expose-server/stargazers) - SSH-based open source tool, with no configuration or installation, distributed worldwide, to expose your local services. Uses your GitHub username and public SSH keys to authenticate you and provide you with a short personalised URL. AGP-3.0 License. Written in Python.
*   [srv.us](https://docs.srv.us/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/7a0e3e65-1b8d-42e9-b620-85fef141b837.svg%2Bxml?raw=true)
    ](https://github.com/pcarrier/srvus/stargazers) - SSH-based. Terminates TLS. Hostnames based on your key, optionally GitHub and/or GitLab username. 0BSD License. Written in Go.
*   [holepunch](https://github.com/CypherpunkArmory/holepunch) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/ddedb941-7ed4-4923-8426-56227bbc1fab.svg%2Bxml?raw=true)
    ](https://github.com/CypherpunkArmory/holepunch/stargazers) - Uses SSH for muxing. Domain has expired. AGP-3.0 Licensed. Written in Python.
*   [docker-wireguard-tunnel](https://github.com/DigitallyRefined/docker-wireguard-tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/f96c1964-e9da-45f5-88d2-cd1cbd97be17.svg%2Bxml?raw=true)
    ](https://github.com/DigitallyRefined/docker-wireguard-tunnel/stargazers) - Connect two or more Docker servers together sharing container ports between them via a WireGuard tunnel.
*   [cactus-tunnel](https://github.com/jeffreytse/cactus-tunnel) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/f960672f-17fc-4301-9bd2-73c0b42d4862.svg%2Bxml?raw=true)
    ](https://github.com/jeffreytse/cactus-tunnel/stargazers) - 🌵 A charming TCP tunnel over WebSocket and Browser. Written in TypeScript.
*   [chiSSL](https://github.com/NextChapterSoftware/chissl) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/8b00996b-60e5-4811-a2a2-efe6e99f4cf2.svg%2Bxml?raw=true)
    ](https://github.com/theborakompanioni/ngtor/stargazers) - Lightweight version of Chisel that allows you to expose local servers running on your development machine to the internet with valid SSL certificates. MIT License. Written in Go.
*   [specter](https://github.com/zllovesuki/specter) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/fbccb336-f640-4166-a1c9-9fc59461a4f7.svg%2Bxml?raw=true)
    ](https://github.com/zllovesuki/specter/stargazers) - Interesting approach utilizing a DHT. QUIC transport. MIT License. Written in Go.
*   [tnnlink](https://github.com/LiljebergXYZ/tnnlink) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/9faba389-b056-411b-8fde-595443ff7387.svg%2Bxml?raw=true)
    ](https://github.com/LiljebergXYZ/tnnlink/stargazers) - SSH-based. Golang. Not maintained.
*   [ngtor](https://github.com/theborakompanioni/ngtor) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/a2ed026f-c942-476b-abae-05a6dd672f95.svg%2Bxml?raw=true)
    ](https://github.com/theborakompanioni/ngtor/stargazers) - Easily expose local services via Tor. Written in Java.
*   [Punchmole](https://github.com/Degola/punchmole/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/054bbd6a-8a3e-4463-965b-241dab122bbf.svg%2Bxml?raw=true)
    ](https://github.com/Degola/punchmole/stargazers) - Can be integrated directly into an existing Node.js project. Written in JavaScript.
*   [ephemeral-hidden-service](https://github.com/aurelg/ephemeral-hidden-service) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/a0ffa371-14e7-4740-8528-97061560f969.svg%2Bxml?raw=true)
    ](https://github.com/aurelg/ephemeral-hidden-service/stargazers) - Create ephemeral Tor hidden services from the command line. Written in Python.
*   [netmask](https://github.com/josephdove/netmask) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/415bc739-83c2-4575-a270-891a789c4c1b.svg%2Bxml?raw=true)
    ](https://github.com/josephdove/netmask/stargazers) - A TCP/UDP self-hostable network tunneling solution that supports IPv4 and IPv6. Client has a GUI. MIT License. Written in Python.
*   [tunnelite](https://github.com/cristipufu/tunnelite) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c8b7d1a8-2fa0-4606-bb18-09d6efb28c62.svg%2Bxml?raw=true)
    ](https://github.com/cristipufu/tunnelite/stargazers) - A self-hostable tunneling solution for TCP, HTTP and WS connections over websockets. CLI client. MIT License. Written in .NET.
*   [mmar](https://github.com/yusuf-musleh/mmar) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c27683dd-a11d-414f-beba-7a9894d83510.svg%2Bxml?raw=true)
    ](https://github.com/yusuf-musleh/mmar/stargazers) - A zero-dependency, self-hostable, cross-platform HTTP tunnel that exposes your localhost to the world on a public URL. AGPL-3.0 License. Written in Go.

*   [ngrok 2.0](https://ngrok.com/) - Probably the gold standard and most popular. Closed source. Lots of features, including TLS and TCP tunnels. Doesn't require root to run client.
*   [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup) - Excellent free option. Nicely integrates tunneling with the rest of Cloudflare's products, which include DNS and auto HTTPS. Client [source code](https://github.com/cloudflare/cloudflared) is Apache 2.0 licensed and written in Golang.
*   [Microsoft Dev Tunnels](https://learn.microsoft.com/en-us/azure/developer/dev-tunnels/overview) - Not as useful for self-hosting (no custom domains and it shows warnings when people visit the URLs), but a solid option for dev work.
*   [Livecycle Docker Extension](https://hub.docker.com/extensions/livecycle/docker-extension) - Offer much more than just tunneling. Have a collaboration layer (Dashboard) that allows you to bring collaborations, debug, and gather feedback from the people you are working with. Share HTTPS URLs.
*   [Beeceptor](https://beeceptor.com/local-tunnel/?ref=awesome-tunneling) - Goes beyond tunneling. Rest API mocking and intercepting tool. You can view the live requests and send mocked responses. Written in JavaScript.
*   [Pinggy](https://pinggy.io/) - SSH based single command HTTPS / TCP / TLS tunnels, no downloads required. Rich terminal interface and a web debugger. Free tier - 60 min timeout. The paid tier allows custom domains with built-in Let's Encrypt certificates.
*   [Loophole](https://loophole.cloud/) - Offers end-to-end TLS encryption with the client automatically getting certs from Let's Encrypt. QR codes for URL sharing. The client is open source. Can serve a local directory over WebDAV. MIT License. Written in Go.
*   [localhost.run](https://localhost.run/) - Simple hosted SSH option. Supports custom domains for a cost.
*   [Packetriot](https://packetriot.com/) - Comprehensive alternative to ngrok. HTTP Inspector, Let's Encrypt integration, doesn't require root and Linux repos for apt, yum and dnf. Enterprise licenses and self-hosted option.
*   [Horizon Tunnel](https://hrzn.run/) - Easy to use HTTP(S) and websocket tunneling aimed at development. Free tier available. Fixed URL is part of paid plans.
*   [Hoppy](https://hoppy.network/) - WireGuard-based. Provides static IPv4 and IPv6 addresses for your machines, which is a simple and useful level of abstraction. Targeted towards self-hosters and people behind NATs.
*   [gw.run](https://gw.run/) - Specifically focusing on securely exposing internal web apps to a group of people; not for publicly facing apps. Share access via email address then allow users to log in with common login providers like Google.
*   [SSHReach.me](https://sshreach.me/) - Paid SSH-based option. Uses a simple Python script.
*   [KubeSail](https://kubesail.com/) - Company offering tunneling, dynamic DNS, and other services for self-hosting with Kubernetes.
*   [inlets](https://inlets.dev/) - Used to be [open source](https://github.com/inlets/inlets-archived); now focused on a polished commercial offering. Designed to work well with Kubernetes.
*   [LocalToNet](https://localtonet.com/) - Supports UDP. Free for a single tunnel. Paid supports custom domains.
*   [LocalXpose](https://localxpose.io/) - Looks like a solid paid option, with a limited free tier.
*   [playit.gg](https://playit.gg/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/9a05fa43-48c5-45cb-935b-3042b49bc869.svg%2Bxml?raw=true)
    ](https://github.com/playit-cloud/playit-agent/stargazers) - Specifically marketed as tunneling for game servers. Client is open source. Server is not. Has a free tier. TCP and UDP supported. Custom domains and dedicated IPs available. Client written in Rust.
*   [Tabserve.dev](https://tabserve.dev/) - Web UI that runs entirely in the browser and uses a Cloudflare Worker for https.
*   [Serveo](https://serveo.net/) - SSH-based, signup optional, offering HTTP(S) and TCP tunneling and SSH jump host forwarding capabilities.
*   [Homeway](https://homeway.io/) - Secure and private remote access for Home Assistant. The free tier has a monthly data limit cap, but unlimited data is only $2.49/month.
*   [btunnel](https://www.btunnel.in/) - Expose localhost and local tcp server to the internet. The free plan includes file server, custom http request and response headers, basic auth protection and 1 hour tunnel timeout.
*   [remote.it](https://www.remote.it/) - Tunnels SSH, HTTP/S, TCP, Docker, popular database etc. allows mapping a local port to a remote port.
*   [StaqLab Tunnel](https://tunnel.staqlab.com/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/ade48999-433d-47b3-a777-297fd72c6fa2.svg%2Bxml?raw=true)
    ](https://github.com/abhishekq61/tunnel-client/stargazers) - SSH-based. The client is open source. The server doesn't appear to be.
*   [LocalCan](https://www.localcan.com/) - MacOS app for exposing local apps, has custom domains with built-in Let's Encrypt certificates. It also can publish .local domains on the local network.
*   [Openport.io](https://openport.io/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/ff68cbcd-926c-472e-b892-a5d4e9a09bf3.svg%2Bxml?raw=true)
    ](https://github.com/openportio/openport-go/stargazers) - Open-source client, written in Go. Supports HTTP(S) and TCP. REST Api. No account needed. Web dashboard. Also works on ESP32.
*   [Lokal.so](https://lokal.so/?ref=awesome-tunneling) HTTP/TCP/UDP Tunneling & Debugging, zero-config .local address with https, built-in S3 Server, AI Assistant, available as Desktop GUI, Web, REST API, and \*CLI, available on Mac, Windows and Linux.

*   [headscale](https://github.com/juanfont/headscale) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/d079ef32-4462-436d-9847-44a0c6a32909.svg%2Bxml?raw=true)
    ](https://github.com/juanfont/headscale/stargazers) - Open source implementation of Tailscale control server. Can be used with Tailscale's official open source client. Written in Go.
*   [Tailscale](https://www.tailscale.com/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/d3d19e3e-dde3-433c-bc84-b0cf7c525cc9.svg%2Bxml?raw=true)
    ](https://github.com/tailscale/tailscale/stargazers) - Built on WireGuard. Easy to use. Control server is closed source. Client [code](https://github.com/tailscale) available with a BSD3 license + separate patents file.
*   [Teleport](https://goteleport.com/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/ec590342-45fc-4942-a45b-7a8aae57dca0.svg%2Bxml?raw=true)
    ](https://github.com/gravitational/teleport) - Comprehensive control plane tool, but also supports [accessing apps](https://goteleport.com/docs/application-access/introduction/) behind NATs. Written in Go.
*   [Nebula](https://github.com/slackhq/nebula) - [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/5cf347a7-cd3f-4d9d-a7c9-2c03fad13e09.svg%2Bxml?raw=true)
    ](https://github.com/zerotier/slackhq/nebula) Peer-to-peer overlay network. Developed and used internally by Slack. Similar to Tailscale but completely open source. Doesn't use WireGuard. Written in Go.
*   [ZeroTier](https://www.zerotier.com/) - [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/7f12f1b5-8b44-4c46-8bdc-2fc254c0f594.svg%2Bxml?raw=true)
    ](https://github.com/zerotier/ZeroTierOne/stargazers) Layer 2 overlay network. They take decentralization seriously, and like to say "decentralize until it hurts, then centralize until it works." Written in C++.
*   [Netmaker](https://github.com/gravitl/netmaker) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/c48857d7-2fd4-4c90-a450-8687892466c5.svg%2Bxml?raw=true)
    ](https://github.com/gravitl/netmaker/stargazers) - Layer 3 peer-to-peer overlay network and private DNS. Similar to Tailscale, but with a self-hosted server/admin UI. Runs kernel WireGuard so very fast. Apache 2.0 License. Written in Go.
*   [NetBird](https://github.com/netbirdio/netbird) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/423e42d0-6388-44c2-ae62-3168146d54fd.svg%2Bxml?raw=true)
    ](https://github.com/netbirdio/netbird/stargazers) - NetBird is an open-source VPN management platform built on top of WireGuard® making it easy to create secure private networks for your organization or home.
*   [Firezone](https://www.firezone.dev/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/6c09e65b-c1e1-4321-b989-980552e1c29e.svg%2Bxml?raw=true)
    ](https://github.com/firezone/firezone) - Layer 3/4 overlay network. Runs on kernel WireGuard® and supports SSO using generic OIDC/SAML connectors. Distributed under Apache 2.0 license and written in Elixir/Rust.
*   [n2n](https://www.ntop.org/products/n2n/) - [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/b51808d8-d793-4be9-a6ae-f61af2f23d8c.svg%2Bxml?raw=true)
    ](https://github.com/ntop/n2n/stargazers) - Built on nodes and supernodes. GPL-3.0 license. Written in C.
*   [innernet](https://github.com/tonarino/innernet) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/09428589-956d-497b-937f-761d622c6a7e.svg%2Bxml?raw=true)
    ](https://github.com/tonarino/innernet/stargazers) - Similar to Netmaker, Nebula, and Tailscale. Takes advantage of existing networking concepts like CIDRs and the security properties of WireGuard to turn your computer's basic IP networking into more powerful ACL primitives. Written in Rust.
*   [Portals for Mac](https://github.com/build-trust/ockam/blob/develop/examples/app/portals/README.md) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/07dc9879-ee84-45e4-a9ef-70c68ad0194e.svg%2Bxml?raw=true)
    ](https://img.shields.io/github/stars/build-trust/ockam/stargazers) - A Mac app that uses the [Ockam](https://github.com/build-trust/ockam) library to privately share a service on your Mac to anyone, anywhere. The service is shared securely over an end-to-end encrypted Ockam Portal. Apache 2.0 License. Written in Rust.
*   [Pritunl](https://pritunl.com/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/7c3f901a-3f5e-4f1e-bad2-61f9e5925492.svg%2Bxml?raw=true)
    ](https://github.com/pritunl/pritunl/stargazers) - Seems quite comprehensive and complicated. OpenVPN, WireGuard, and IPSec support.
*   [Tinc](https://github.com/gsliepen/tinc) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/0e512a60-56f5-4df8-8ca6-e632cd9467ba.svg%2Bxml?raw=true)
    ](https://github.com/gsliepen/tinc/stargazers) - Tinc is a peer-to-peer VPN daemon that supports VPNs with an arbitrary number of nodes. Instead of configuring tunnels, you give Tinc the location and public key of a few nodes in the VPN. After making the initial connections to those nodes, tinc will learn about all other nodes on the VPN, and will make connections automatically. When direct connections are not possible, data will be forwarded by intermediate nodes. Written in C.
*   [OpenZiti](https://openziti.github.io/) - [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/8ac984d3-4928-431a-941e-7f3417850fe8.svg%2Bxml?raw=true)
    ](https://github.com/openziti/ziti/stargazers) - Overlay network. The goal of OpenZiti is to extend zero trust all the way into your application, not just to your network. Apache 2.0 license. Written in Go.
*   [weron](https://github.com/pojntfx/weron) - [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/78b77f08-ce0e-4910-85c8-d2d373ad0a9d.svg%2Bxml?raw=true)
    ](https://github.com/pojntfx/weron/stargazers) - Built on WebRTC. Can create Layer 2 and Layer 3 networks. NAT traversal via STUN and TURN. AGPL-3.0 license. Written in Go.
*   [bifrost](https://github.com/aperturerobotics/bifrost) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/a57d065f-7358-4310-8d3a-2a5f55c6f8b8.svg%2Bxml?raw=true)
    ](https://github.com/aperturerobotics/bifrost/stargazers) - Bifrost is a peer-to-peer communications engine with pluggable transports. It supports dynamic configuration of transports, listeners, forwarding rules, and can tunnel other protocols over WebRTC and Quic. Apache 2.0 License. Written in Go.
*   [Ngrok-operator](https://github.com/zufardhiyaulhaq/ngrok-operator) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/b6f8a20f-c926-46bd-8b1b-a5875a4e28e3.svg%2Bxml?raw=true)
    ](https://github.com/zufardhiyaulhaq/ngrok-operator/stargazers) - Ngrok but integrated with Kubernetes, allows developers on private Kubernetes to easily access their services via Ngrok.
*   [chisel-operator](https://github.com/FyraLabs/chisel-operator/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/1b46728f-0ccc-4b1c-93bd-6b9c44629601.svg%2Bxml?raw=true)
    ](https://github.com/FyraLabs/chisel-operator/stargazers) - Kubernetes integration for Chisel. Similar functionality to inlets. MIT License. Written in Rust.
*   [frp-operator](https://github.com/zufardhiyaulhaq/frp-operator) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/6c13025c-8e65-44a2-975c-2e80130b1dab.svg%2Bxml?raw=true)
    ](https://github.com/zufardhiyaulhaq/frp-operator/stargazers) - Kubernetes integration for [FRP](https://github.com/fatedier/frp). MIT License. Written in Go.
*   [Mycoria](https://mycoria.org/) [![](https://github.com/pfurini/web-clipper/blob/main/images/7-10-2025,%2014-20-20/8d88a5ca-fd74-4cbc-8b7e-f69ba4343fdc.svg%2Bxml?raw=true)
    ](https://github.com/mycoria/mycoria/stargazers) - Overlay network where the IPv6 address is the key: Easily share address + public key via a DNS AAAA record or map names locally. Secure by default (firewall included). BSD-3 license. Written in Go.

*   [Roll your own Ngrok with Nginx, Let's Encrypt, and SSH reverse tunnelling](https://jerrington.me/posts/2019-01-29-self-hosted-ngrok.html)
*   [Poor man's ngrok with tcp proxy and ssh reverse tunnel](https://dev.to/k4ml/poor-man-ngrok-with-tcp-proxy-and-ssh-reverse-tunnel-1fm)
*   [How I built Ngrok Alternative (jprq)](https://dev.to/azimjohn/how-i-built-ngrok-alternative-3n0g)
*   [Great SO answer by AJ ONeal about how these things work](https://stackoverflow.com/a/52614266/943814)
*   [Talk by AJ ONeal about tunneling tech](https://youtu.be/E1Q2MWGCADo)
*   [ngrok alternative: localtunnel + Caddy + Lets Encrypt](https://morph027.gitlab.io/blog/localtunnel-ngrok/)
*   [Can You Grok It - Another DIY tunnel blog post](https://0xda.de/blog/2024/04/can-you-grok-it/)

*   [HN comment about needing Namecheap + Cloudflare + ngrok](https://news.ycombinator.com/item?id=24475946).