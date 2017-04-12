---
title: What is Server Side
date: 2017-04-12 01:07:17
tags:
---

I've had to explain how the Internet works for many times over the last few years. It's time to write parts of that down.

This time, we're gonna explore the concept of the **server side of websites**.

Let's assume you've fiddled with HTML and at least once created an `.html` file on your computer and viewed it in a browser.

You open your browser of choice, enter `google.com` and in a second you see the Google search page. The page consists of HTML code, sure. But it's different each time based on your search query, Google account, location and so on. It's clearly not just an `.html` file being loaded from somewhere. *Why does it change? How?!*

## Computers talking to each other

I'm gonna oversimplify in this section.

You are connected to either a router or a mobile network tower on a building nearby if you're on data. Whichever it is, we'll call it **the gateway**.

Your device is in blissful ignorance about the outside world. The Internet on the other side of the gateway is as much of an incomprehensible black box to your device as it is to you.

Every computer, laptop and phone only ever communicates to its gateway.

On the other end, there's another machine set up by your ISP, usually a slightly bigger one than your router. Your whole apartment complex or neightbourhood is connected to it. And it too has a gateway of it's own.

We need to go deeper! The next level is also your ISP. This time though, the routing device has not one but multiple paths forward! At this level, the router looks at your `google.com` and decides which way to forward it. It *routes* your request — to another connected neighbourhood, or to another city, or to a gateway of another ISP.

Part of the job of being an ISP is to configure gateways to forward requests closer to specified addresses, one gateway at a time.

*It's gateways all the way down.*

When you want to go to `google.com`, your computer talks to its local network gateway, which sets off a chain reaction through the whole path to another computer. Every domain name like `google.com` is an address of a machine somewhere in the world.

That machine runs software that waits for page requests and responds to them. This machine is called **the server** of a website and this software that generates webpages and responds to requests is called **the server** of the website. Wait, *what?* Yeah...

Sometimes the hardware part is called **the hosting** and the software is called **the server-side**, as opposed to the client-side code that includes HTML and JavaScript executed in your browser.

<details>
<summary>*Huh?* Click here for more details on networking.</summary>

To learn about networks without such gross simpification watch [these 13 short videos by Ben Eater](https://www.youtube.com/playlist?list=PLowKtXNTBypH19whXTVoG3oKSuOcw_XeW) on Youtube. I watched them twice. *For fun*. They are that good.
</details>

## Real example — PHP

Enough talking, let's run server software on your computer!

PHP is a language based on HTML and a program for translating it into HTML.

- You write ordinary HTML but can also use a special tag `<?php ... ?>`, inside which you write in PHP.
- You then process it using the PHP program. It executes PHP code inside those tags and gives you plain HTML that you can send to browsers.

As you can see, no mention of web requests and responding to them.

Hmm... Yeah, we'll need a separate program for that. This one is called Apache. You specify which folder contains the website files and it sends them as responses to browsers. You can also tell it to preprocess certain files with another program before sending. In our case, that's PHP for `.php` files.

<details>
<summary>Download latest [XAMPP](https://www.apachefriends.org/download.html) — a bundle with Apache and PHP included.</summary>

![XAMPP download page](/images/xampp-download-page.png)

*Scroll further on that page for Linux and OS X downloads.*
</details>
<details>
<summary>Install all the things! Actually we only need Apache and PHP. You may deselect everything else.</summary>

![XAMPP installation](/images/xampp-installation.png)
</details>
<details>
<summary>Allow Apache to access networking features of your computer.</summary>

![Windows Firewall complains about Apache](/images/xampp-apache-firewall.png)
</details>
<details>
<summary>Launch the XAMPP Control Panel and start Apache - click "Start" on the Apache row.</summary>

![XAMPP Control Panel](/images/xampp-control-panel.png)
</details>

Now open [http://localhost](http://localhost) in your browser. This address always refers to *this* device - the current one you're using.

<details>
<summary>You should see *"Welcome to XAMPP"*.</summary>

![XAMPP Dashboard Welcome page](/images/xampp-welcome-page.png)
</details>

You may not realize it yet, but you're successfully running Apache with PHP locally — on your computer — and look at a page generated dynamically.

What's the folder where you installed XAMPP? For me it's `C:\xampp`. Go there, open folder `htdocs` inside and delete everything from it. That's the welcome page stuff.

Now open any plain text editor (Notepad, Sublime Text, ...) and create a file `index.php` inside the `htdocs` folder. Insert the following code:

```PHP
<html>
  <body>
    <h2>Hello world!</h2>
    Today is: <b><?php echo(date('Y-m-d')) ?></b>
  </body>
</html>
```

Go to [http://localhost](http://localhost/) again and you should see this:

![XAMPP PHP Hello World](/images/xampp-php-hello-world.png)

It's crucial to understand how this is fundamentally different from ordinary HTML. In this example `<?php ?>` tag is preprocessed by PHP and *is never sent to the browser*. It's already executed and translated to resulting HTML before sending.

Now open XAMPP Control Panel again and click "Stop" next to Apache. Only one program at a time is allowed to respond to web requests. We need to give this spot to Node.js in the example below.

## Real example — Node.js

Let's go! Same instructions, only this time the server-side language is gonna be JavaScript and not PHP.

- Download latest [Node.js](https://nodejs.org/en/download/).
- Install it.
- Create a file `index.js` anywhere you want and open it with a text editor. Paste this code:

```JavaScript
const http = require('http');

const server = http.createServer((req, res) => {
  const date = new Date
  const readableDate = date.getFullYear() + '-' +
    (date.getMonth() + 1) + '-' +
    date.getDate();

  const html = `
    <html>
      <body>
        <h2>Hello world!</h2>
        Today is: <b>${readableDate}</b>
      </body>
    </html>
  `

  res.statusCode = 200;
  res.end(html);
});

const hostname = 'localhost';
const port = 80;

server.listen(port, hostname, () => {
  console.log('Open http://localhost/ in your browser!');
});
```

This source code may be confusing, especially if you're not familiar with JavaScript and features added to it since 2015. It doesn't matter, just follow along. You can read it and hopefully get a gist of what it does.

<details>
<summary>Open the file with Node.js</summary>

![Open the file with Node.js](/images/nodejs-open-file-with-nodejs.png)
</details>

A black command line window should open, which is where your code runs.

Go to [http://localhost](http://localhost/) again and you should see the same result as in the previous example with PHP.

## HTTP

Although it's not clear from the pratical examples above, the actual mechanism of web requests and responses is quite simple.

There's no magic. In fact, web servers and browsers talk in plain text.

Let's have a look at what's going on under the hood when you go to `http://google.com/about`. Your browser sends the following request — yes, as simple text:

```
GET /about HTTP/1.1
Host: google.com
Accept: text/html
Accept-Language: en-us
User-Agent: Mozilla/4.0
```

The server receives the request and responds with this text:

```
HTTP/1.1 200 OK
Date: Wen, 12 Apr 2017 05:56:53 GMT
Server: Apache/2.2.14 (Win32)
Last-Modified: Sat, 20 Nov 2004 01:07:17 GMT
Content-Length: 44
Content-Type: text/html

<html><body><h1>Hello world!</h1></body></html>
```

Well, of course, in reality the response is quite a lot longer since that page is so complex. But the general structure is exactly the same.

The role of Apache and other similar software is to wait for requests, look at them and compose correct responses in this standard format.

## Deployment

> Software deployment is all of the activities that make a software system available for use.
>
> — *[Wikipedia](https://en.wikipedia.org/wiki/Software_deployment)*

When someone says they are deploying a website, it means they already have source code (PHP, JavaScript or any other) ready and are in the process of:
- uploading it to the machine that acts as the server
- and then launching/updating the server software.

## Further reading

Just google what you wanna know, lol.
