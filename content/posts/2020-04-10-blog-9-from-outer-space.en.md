+++
title = "Blog 9 from Outer Space"
date = 2020-04-10T01:27:00-05:00
slug = "blog-9-from-outer-space"
draft = false
+++

Recently, I've been thinking about ways to unify my micro blog entries with my current site, and I've been reconsidering the ideas from [IndieWeb](https://indieweb.org/): unlike [ActivityPub](https://activitypub.rocks/) (the protocol Mastodon, Pleroma and the likes use for federation), which seems to want everything be done dynamically via server APIs and JSON responses, the various standards recommended by the IndieWeb community allows machine readable feed to be generated straight from a static HTML file correctly marked-up. A core idea that IndieWeb seem to implicitly rely on is the lifetime of the URIs, and to a greater extent, site owner's control over the domain name. Withe the recent [drama](https://www.eff.org/deeplinks/2020/03/members-congress-once-again-urge-icann-save-dot-org) regarding the .ORG domain, I came to realize that a future in which domain names are too expansive to maintain (or are subject to seizures by various entities) may not actually be too distant, and this could seriously undermine the entire premise IndieWeb is built upon, not to mention the a lot more common link rots. Fortunately, I think the [IPFS](https://ipfs.io/) (InterPlanetary File System) has the potential to solve both problems.


## A Crash Course on IPFS {#a-crash-course-on-ipfs}

Now, now, I know IPFS has a really buzz-wordy vibe when compared to say the [Dat protocol](https://dat.foundation/), [pingfs](https://github.com/yarrick/pingfs), or even [Scruttlebutt](https://scuttlebutt.nz/), and the various cryptocurrency start-ups that bundle IPFS and all kinds of acronyms in their marketing materials surely doesn't do it any favors, but it does seem like the most established and ready-to-use. Here's my best attempt at explaining IPFS, with information mostly obtained from the [official documentation](https://docs.ipfs.io/) and [this talk](https://www.youtube.com/watch?v=HUVmypx9HGI). In case you are interested in further implementation details, [this session from IPFS Camp 2019](https://www.youtube.com/watch?v=Z5zNPwMDYGg) is a great resource.

A simplified interpretation of link to an web page is but a fancy way to point to a file on some server. Just like path to a file, the link would be unreachable if the server is down, even if someone sitting in the same room might have the contents cached. In IPFS, files (or data blocks) are addressed by corresponding cryptographic hashes of their contents, and stored in a distributed fashion across all peers. This means no centralized facility is required to access the files, file integrity can be easily verified, P2P sharing can be used to speed up access, and files stored this way are inherently immutable.

Not being able to change files seems like a rather large price to pay, but just like any other problem in computer science, this can be solved by adding a layer of abstraction. IPNS (InterPlanetary Name System) utilizes public-key cryptography to create immutable addresses that can point to different files. An IPNS address is basically the hash of a public key. An IPNS lookup would involve searching for files (each containing an IPFS address) signed by the corresponding private keys, identifying the most recent one, and redirecting to the correct file. To utilize IPNS, the user would start by creating a public-private key pair, followed by uploading desired files into IPNS, and sign and upload a pointer file containing IPFS address to the uploaded content. When an update is desired, the user only need to sign and upload another pointer file to the new location.

A lot of ideas used in IPFS has been explored before by projects like BitTorrent (peer-to-peer sharing), [Fossil](https://en.wikipedia.org/wiki/Fossil%5F(file%5Fsystem)) and [Venti](https://en.wikipedia.org/wiki/Venti) from Plan9 (write-once data blocks and path redirection), git (Merkle tree), etc. However, the killer feature is how easily IPFS integrates with existing infrastructure. Not only are there HTTP gateways that allows for accessing IPFS/IPNS from web browsers instead of IPFS clients, but also compatibility with FUSE (Filesystem in Userspace), which actually allows you to mount the entire IPFS as a read-only partition (sure this also makes hosting static websites possible, but you have to admit that having access to a global-scale P2P shared drive is way cooler).


## Hosting Static Websites on IPFS {#hosting-static-websites-on-ipfs}

The [official guide](https://docs-beta.ipfs.io/how-to/command-line-quick-start/) already outlines the general usage pattern pretty well. Here's the TLDR:

-   Run `ipfs init` and `ipfs daemon` to initialize and start the IPFS client.
-   Generate the website files and run `ipfs add -r <website-root>` to send its contents onto the IPFS. The last few lines of the output should tell you the hash for the root directory.
-   If you want to make use of IPNS, run `ipfs name publish <website-root-hash>` to direct the IPNS link to the folder you just uploaded. The IPNS address can be obtained via `ipfs id | grep ID`.
-   Repeat the last two steps every time and the website files are updated or rebuilt. The process has little overhead due to the inherent deduplication in addressing, making it particularly suitable for static sites where smaller files (HTML and CSS files) tend to change more often than larger files (photos and other media files).

Once this is done, you can access your website at either `<gatway-address>/ipfs/<website-root-hash>` or `<gatway-address>/ipns/<ipns-address>` from any HTTP gateway: you can use the local one (likely at `127.0.0.1:8080`) started by the IPFS daemon, or any of [the public ones](https://ipfs.github.io/public-gateway-checker/) (comes with extra risk of MITM attacks from the gateway owners as file retrieval is done on the gateway servers). You can also make use of the [DNSLink](https://docs.ipfs.io/guides/concepts/dnslink/) functionality, by adding a TXT record to your existing domain:

```text
dnslink=/ipfs/<website-root-hash>
```

or

```text
dnslink=/ipns/<ipns-address>
```

This would allow access to the website from the gateways via `/ipns/<domain-name>`, easier to remember for most. For instance, you can now access this site using the `ipfs.io` gateway [here](https://ipfs.io/ipns/shimmy1996.com/).

Do note that because the additional IPFS address in the URL when accessing the files from HTTP gateways, we need to use relative URLs in the generated files. In Hugo, this can be achieved by setting

```toml
relativeURLs = true
```

Of course, being a P2P network, IPFS won't be able to retrieve the files if there is no copy to work with at all. By default, IPFS client would [pin](https://docs.ipfs.io/guides/concepts/pinning/) anything you shared from the local machine: pinned contents won't get deleted, ensuring at least one copy of the shared content is available on IPFS. You can unpin outdated versions of the website, or if you want, find and pin the shared directory on multiple machines for some redundancy.


## The Stars, Like Dust {#the-stars-like-dust}

Back to the issue with IndieWeb: the increasingly shady domain name system and link rots makes URI stability in HTTP is hard to maintain. However, what if we use IPFS/IPNS addresses as URIs? It's a match made in heaven: we get robust distributed access to static web pages, gated by Mathematics instead of FBI warnings. Removing the need for maintaining a server also lowers the barrier of entry of owning a website. The HTTP protocol has existed for 29 years, and IPFS, only 5. I don't know if IPFS will continue to exist for the next 24 years to come, but if it does, I'm sure we will be looking at a more robust, perhaps more chaotic, but more lively and colorful online world.
