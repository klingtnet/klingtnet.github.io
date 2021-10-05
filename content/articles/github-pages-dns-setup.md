```json
{
  "author": "Andreas Linz",
  "title": "GitHub Pages DNS Setup",
  "description": "In this article I describe the DNS setup for my GitHub Pages website which is served under the www subdomain and the apex or base domain.",
  "created_at": "2021-10-04",
  "tags": null,
  "hidden": false
}
```

# GitHub Pages DNS Setup

I chose [GitHub Pages][ghp] to serve [this website][this-website].  By default a GitHub Pages website is served under `<repo>.github.io`, which is fine but now what I wanted.  As this is a replacement for my old website I wanted it to be served under my domain `klingt.net`.

Fortunately, setting up a GitHub pages website is pretty easy.  Just create a repository and set the branch and directory which contains the website files in the repositories Pages settings.  [^1]

![GitHub Pages settings source dialog](/articles/github-pages-settings-source-dialog.png)

Adding a custom domain is also straightforward and [GitHub's excellent documentation has you covered][ghp-domain] as usual.  For my case I wanted to serve this website to be served under the `www` subdomain, that is `www.klingt.net`, and the apex or base domain `klingt.net`.  Configuring an apex domain requires an additional step which is why I decided to write this article.

First things first, let's start with setting up the required DNS records.  For the [`www` subdomain][ghp-www] I just needed to add a `CNAME` record that points `www.klingt.net` to `klingtnet.github.io`.  I use `dig` for debugging or showing DNS records and in the following command you will see how to retrieve a `CNAME` record for a domain:

```sh
$ dig +noall +answer www.klingt.net CNAME
www.klingt.net.         3600    IN      CNAME   klingtnet.github.io.
```

If you do not have a shell available, e.g. because you're reading this article from a phone or from a Windows machine :roll_eyes:, you can use online tools liks <https://dns-lookup.jvns.ca>.

Setting up the [apex domain][ghp-apex] is a bit more complicated because there are no `CNAME` records for this type of domain.  Instead, you can just define an [`ALIAS`][alias] record that—as the name suggests—alias `klingt.net` with `www.klingt.net`.  Let's do a DNS query using `dig` for the `ALIAS` record:

```sh
$ dig +noall +answer klingt.net ALIAS
klingt.net.             929     IN      A       185.199.109.153
klingt.net.             929     IN      A       185.199.111.153
klingt.net.             929     IN      A       185.199.108.153
klingt.net.             929     IN      A       185.199.110.153
```

You might wonder why the `ALIAS` record is not showing up in the result.  This is perfectly fine because the name server resolves an ALIAS directly into A (IPv4) or AAAA (IPv6) records.

The last thing you need to do is to set the default domain under which your domain should be served, again in the Pages settings dialog of the GitHub repository:

![GitHub Pages settings domain dialog](/articles/github-pages-settings-domain-dialog.png)

Doing this will create a [`CNAME` file][cname-file] in the source branch that you've initially configured.  Make sure to pull the commit that created this file or manually add it to your website contents.

Et Voilà!  Your website should now be served under your custom domain with a valid HTTPS certificate.

[this-website]: https://github.com/klingtnet/klingtnet.github.io
[ghp]: https://guides.github.com/features/pages/
[ghp-domain]: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages#supported-custom-domains
[ghp-www]: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages#supported-custom-domains
[ghp-apex]: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages#using-an-apex-domain-for-your-github-pages-site
[alias]: https://support.dnsimple.com/articles/alias-record/
[cname-file]: https://github.com/klingtnet/klingtnet.github.io/blob/4bd965fa2772ccfc3c69bc5d22ed76d2b9fc65f0/static/CNAME

[^1]: Configuring the source branch and directory is something I always forget to do and then I'm wondering why my website does not show up :sweat_smile: