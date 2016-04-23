---
layout: default
---

# $ cat about.txt
{:id="about"}

We are an information security research group. As we learn more and more about
this field, we intend to share the knowledge acquired during the learning process
and publish our results, ofcourse never forgetting to help the free software community
and to fight for a world without agressive/disrespectful mass surveillance.

We believe that the world can and should be free. If the hacking is the way to do that,
then we will hack it :)

GPG Fingerprint: [9ECB E16A 5FD7 832D 11BB  3F49 661D 7781 12CB F8C7](/assets/public.key.txt)

# $ cat contact.txt
{:id="contact"}

E-mail:

> lampiaosec[at]riseup[dot]net

Facebook:

> [https://facebook.me/lampiaosec](https://fb.me/lampiaosec)

GitHub:

> [https://github.com/lampiaosec](https://github.com/lampiaosec)

IRC:

> \#lampiaosec at OFTC

# $ cat team.txt
{:id="team"}

[+] gjuniioor

> gjuniioor[at]protonmail[dot]ch

> [https://github.com/gjuniioor](https://github.com/gjuniioor)

> [https://gjuniioor.github.io](https://gjuniioor.github.io)

[+] keso

> ricardokeso[at]ricardokeso[dot]com

> [https://github.com/ricardokeso](https://github.com/ricardokeso)

> [http://www.ricardokeso.com](http://www.ricardokeso.com)

[+] userx

> user_x[at]riseup[dot]net

> [https://github.com/UserXGnu](https://github.com/UserXGnu)

> [https://userxgnu.github.io](https://userxgnu.github.io)

# $ cat projects.txt
{:id="projects"}

[+] [Virada Hacker](https://lampiaosec.github.io/virada-hacker/) - a night to code, hack and drink

# $ cat tools.txt
{:id="tools"}

[+] [virgulino](https://github.com/lampiaosec/virgulino) - steganography

[+] [jackthestripper](https://github.com/lampiaosec/jackthestripper) - MITM Attack

# $ cat talks.txt
{:id="talks"}

[+] Crypto [+]

> [Crypto Basic](http://lampiaosec.github.io/talks/Cripto/index.html) at Virada Hacker

[+] Network [+]

> [DNS](https://lampiaosec.github.io/talks/DNS-o-que-voce-precisa-saber/index.html) at Virada Hacker

[+] Privacy [+]
    
> ["SI e Privacidade"](https://gjuniioor.github.io/talks/seguranca-da-informacao-privacidade/) at Semana CTS

# $ cat posts.txt
{:id="posts"}

> GnuPG - [Pt_br](https://gjuniioor.github.io/blog/gnupg/)

{% for post in site.categories.posts %}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

# $ cat articles.txt
{:id="articles"}

{% for article in site.categories.articles %}
* [{{ post.article }}]({{ post.article }})
{% endfor %}
