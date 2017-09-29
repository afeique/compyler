
# Webpage2html: Python 3

An attempt at a Python 3 conversion of [zTrix's webpage2html](https://github.com/zTrix/webpage2html). Instructions on performing the port are provided below. This conversion _seemed_ to work, but it is not extensively tested. I began to run into issues with the converted version.

## Conversion Process

    $ 2to3 -w webpage2html.py

Search for lines utilizing `base64.b64encode()`. Append `.decode("ascii")` to these lines to get something like:

    `base64.b64encode(data).decode("ascii")`

See lines 141, 204, 222.

Replace `js_str.find(<str>)` with `js_str.find(bytes(<str>,"ascii"))`. See lines 221, 223.

To ensure that htmlentities are not incorrectly encoded and lost, change line 322 from

    sys.stdout.write(rs)
    
to

    sys.stdout.buffer.write(rs.encode("utf-8"))
    
See:
https://stackoverflow.com/questions/3597480/how-to-make-python-3-print-utf8

# Webpage2html: Original Documentation

[![Build Status](https://travis-ci.org/zTrix/webpage2html.png)](https://travis-ci.org/zTrix/webpage2html)

## Webpage2html: Save web page to a single html file

This is a simple script to save a web page to a single html file. No mhtml or pdf stuff, no xxx_files directory, just one single readable and editable html file.

The basic idea is to insert all css/javascript files into html directly, and use base64 data URI for image data.

## Usage and Example

save webpage directly from url(**recommended** way):

```bash
$ python2 webpage2html.py http://www.google.com > google.html
```

or save webpage first using browsers such as chrome, to something.html with something_files directory beside.

```bash
$ python2 webpage2html.py /path/to/something.html > something_single.html
```

But note that, the second method may not always work as expected, because there may be urls like `//ssl.gstatic.com/gb/images/v1_c69d5271.png` (from google index page), but the file is missing in `Google_files` directory saved by browsers.

Enable javascript, for example, save 2048 game page into a single html for offline playing

```bash
$ python2 webpage2html.py -s http://gabrielecirulli.github.io/2048/ > 2048.html
```

## Dependency

BeautifulSoup4, lxml, termcolor(optional)

```bash
$ pip install -r requirements.txt
```

or install them manually

```bash
$ pip install lxml BeautifulSoup4 requests termcolor
```

I have tried the default `HTMLParser` and `html5lib` as the backend parser for BeautifulSoup, but both of them are buggy, `HTMLParser` handles self closing tags (like `<br>` `<meta>`) incorrectly(it will wait for closing tag for `<br>`, so If too many `<br>` tags exist in the html, BeautifulSoup will complain `RuntimeError: maximum recursion depth exceeded`), and `html5lib` will encode encoded html entities such as `&lt;` again to `&amp;lt;`, which is definitly unacceptable. I have tested many cases, and `lxml` works perfectly, so I choose to use `lxml` now.

The `termcolor` package is for colored log output support if you like.

## Cases still does not work

### browser side less compiling

The page embeds less css directly and use less.js to compile in browser. In this case, I still cannot find a way to embed the less code into generated html to make it work.

```
<link rel="stylesheet/less" type="text/css" href="http://dghubble.com/blog/theme/css/style.less">
<script src="http://dghubble.com/blog/theme/js/less-1.5.0.min.js" type="text/javascript"></script>
```

 - http://lesscss.org/#client-side-usage
 - http://dghubble.com/blog/posts/.bashprofile-.profile-and-.bashrc-conventions/

# Thanks

 1. Thanks lukin.a.i who submitted patch to fix not recognised css link (rel=stylesheet) issue

# License

[webpage2html] use [SATA License](LICENSE.txt) (Star And Thank Author License), so you have to star this project before using. Read the [license](LICENSE.txt) carefully.

# Reference

 1. Java port of this project. https://github.com/cedricblondeau/webpage2html-java

[webpage2html]:https://github.com/zTrix/webpage2html


