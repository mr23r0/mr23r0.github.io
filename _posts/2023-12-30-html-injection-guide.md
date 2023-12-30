---
layout: post
category: guides
title: "01 HTML Injection | Web 101"
description: "Mastering Web Penetration Testing, Part 01 - HTML Injection"
---

### Index
- [Introduction](#introduction)
- [HTML](#html)
- [Weaponize](#weaponize)
- [Detection](#detection)
- [Bypass](#bypass)
- [RW Examples](#rw-examples)

### [Introduction](#introduction)
In the realm of web security, understanding vulnerabilities is crucial. HTML Injection stands as a pivotal point in this landscape, representing a vulnerability that can compromise the integrity of web applications.
This technique, while seeming innocuous at first glance, poses a significant threat by allowing attackers to manipulate a webpage's content, structure, or functionality. For web testers and security professionals, comprehending HTML Injection is paramount. It serves as a means to evaluate and fortify web applications, ensuring they are robust enough to withstand such attacks. 

### [HTML](#html)
HTML, or Hypertext Markup Language, is the standard language used to create web pages. HTML is composed of various elements and tags that define the structure of a web page.
```
<!DOCTYPE html>
<html>
<head>
    <title>My Web Page</title>
</head>
<body>

    <h1>Welcome to My Page!</h1>
    
    <p>This is a paragraph of text.</p>
    
    <img src="https://images.theconversation.com/files/450362/original/file-20220307-121610-zfik1f.jpeg" alt="A beautiful image">
    
    <a href="https://en.wikipedia.org/wiki/Dead_Poets_Society">Visit Dead Poets Socity</a>

</body>
</html>

```
This basic HTML structure creates a webpage:

- `<!DOCTYPE html>` declares the document type and version of HTML being used (in this case, HTML5).
- `<html>` encapsulates the entire content of the web page.
- `<head>` contains meta-information about the document, such as the page title, character set, etc.
- `<title>` sets the title of the webpage, which appears in the browser's title bar or tab.
- `<body>` contains the visible content of the webpage.
- `<h1>` is a heading tag used to create the main header.
- `<p>` creates a paragraph of text.
- `<img>` inserts an image with the specified source (`src`) and alternative text (`alt`).
- `<a>` creates a hyperlink to another webpage specified in the `href` attribute.<br/>

This HTML code, when rendered in a web browser, would display a simple webpage with a heading, a paragraph of text, an image, and a hyperlink.  

### [Weaponize](#weaponize)
Imagine HTML tags as building blocks that structure a webpage. Now, when one tag is placed inside another, it's like nesting these building blocks. This nesting can lead to unintended consequences if not handled properly.

For instance, if a website allows users to input text that gets displayed on the page, and this input isn't properly sanitized (meaning, it's not checked for harmful code), a user might enter HTML tags within their input.

Let's say a user innocently types something like `<b>Hello</b>` in a comment section where bold text is not allowed through rich text editor. If the website doesn't filter or neutralize these tags, the entire content following `<b>` will become bold until it reaches `</b>`. This is harmless in most cases and is called HTML injection, but it becomes a problem when malicious users deliberately input code that can harm the website or its visitors.

For instance, an attacker might input JavaScript code within HTML tags. When other users view this comment, their browsers might execute this JavaScript, potentially performing actions like redirecting to malicious sites, stealing session cookies, or displaying unwanted content—things the website's creators never intended.

So, the crux here is that allowing unfiltered HTML tags within other HTML tags on a website's client side (what users see and interact with) can open the door to unexpected and harmful actions if not properly managed or sanitized. It's like allowing someone to rearrange the building blocks in a tower—while most rearrangements might be harmless, some configurations could make the tower unstable or even collapse.

### [Detection](#detection)
Imagine you're a web tester examining a site with input fields—like a search bar or a comment section. Your goal isn't to break things but to find weaknesses that could allow someone with ill intentions to harm the website or its users.

- Testing Input Fields:
 You start by typing harmless text into these fields to ensure they work properly. Then, you get crafty. Instead of regular words, you enter something sneaky: HTML tags or code snippets. For example, you might try typing `<script>alert("Test!")</script>` instead of plain text.
- Playing with Scripts:
 When you hit enter, if a pop-up saying "Test!" appears, you've found a vulnerability. This is like discovering a secret passage in a castle by trying different keys in various doors.
- Sneaky Cross-Site Scripting:
 Now, imagine you can make the website run a script that steals information or alters its content. By entering special code into an innocent-looking comment, you might make it so that when others view it, their browsers unknowingly run harmful scripts.
- Manipulating HTML:
 Sometimes, you try changing things like an image's address or link tags to point somewhere unexpected. It's like pretending to be a magician and making things on the webpage disappear or go to the wrong places.

- Crafty URL Tweaking: 
 Changing the website's address slightly in the browser's address bar might reveal hidden weaknesses. It's akin to altering a treasure map to find hidden paths.

In essence, by playing around with HTML tags and injecting code in various parts of the website, you're like a detective searching for clues. Your goal is to uncover these weaknesses before anyone with harmful intentions does, helping the website's creators strengthen their defenses against potential attacks.

### [Bypass](#bypass)
Developers implement validators and escape mechanisms to protect websites from malicious input. However, attackers often craft specialized payloads to slip through these defenses. These crafted payloads are designed to bypass validators and encoding systems, enabling the injection of harmful HTML code.

These bypass techniques involve manipulating input fields by inserting deceptive HTML tags or using encoded representations of tags. For instance,

- Understanding Input Validation:
 Websites often have forms or input fields where users can submit information. Input validation is the process of checking this information to ensure it's safe before displaying or processing it. If the validation process is weak or nonexistent, attackers can exploit this vulnerability.

- Bypassing Filters:
 Attackers attempt to bypass input filters or validation mechanisms by entering specially crafted strings that evade detection. They might use variations or combinations of HTML tags, special characters, or encoded representations of tags to confuse the validation process.

- Encoding Techniques:
 Attackers might use encoding techniques like URL encoding (`%3Cscript%3E`) or character encoding (`&lt;script&gt;`) to disguise HTML tags and bypass filters that are looking for specific patterns of code.

- Context-Specific Attacks:
 Injection attacks can be context-specific. For instance, if a website allows certain HTML tags in comments but not in usernames, an attacker might try injecting malicious code in the allowed comment section to later affect other parts of the website where HTML injections are not expected.

- Polyglot Injection:
 This technique involves crafting a string that is both valid in HTML and JavaScript. It aims to bypass filters that are focused on HTML while still executing JavaScript code.

- Whitespace Manipulation:
 Injecting code with spaces or newline characters can sometimes confuse filters, allowing malicious code to slip through undetected.

### [RW Examples](#rw-examples)

- [Hackerone](https://hackerone.com/reports/181810){:target="_blank"}
- [Hackerone](https://hackerone.com/reports/1581499){:target="_blank"}
- [Hackerone](https://hackerone.com/reports/1537149){:target="_blank"}
- [Hackerone - Personal Favourite](https://hackerone.com/reports/381553){:target="_blank"}
- [Hackerone - Personal Favourite](https://hackerone.com/reports/150179){:target="_blank"}