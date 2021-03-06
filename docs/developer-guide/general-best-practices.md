---
layout: default
title: General best practices
section: Developer guide
permalink: /docs/developer-guide/general-best-practices/
site_section: developer-guide
---

# General best practices for front end development

Origami modules and services are built to a high standard, following best practices for browser support, accessibility, security and performance. Use of Origami components doesn't mean that you have to follow the same standards, but they are all good practices that will deliver a better experience for our readers, and we strongly encourage you to take a look.

In addition to the rules below, the [Google Web Fundamentals documentation](https://developers.google.com/web/fundamentals) is a good resource for developing best practices.

## Browser support

Conform to the [FT Browser support standard](https://docs.google.com/document/d/1mByh6sT8zI4XRyPKqWVsC2jUfXHZvhshS5SlHErWjXU/edit#heading=h.er7no6jwfim1).  In principle this means that your site will:

* Choose a sensible boundary level between core and enhanced experience
* Not load JavaScript when in core experience (we advocate using a [cuts the mustard]({{site.baseurl}}/docs/developer-guide/modules/core-vs-enhanced-experience) technique)
* Present a consistent, functional and high quality experience to users in both core and enhanced experience


## Design

Although you may spend most of your time on your own product, remember that our readers move from one FT product to another all the time, and we need to make their life as easy as possible by offering a <strong>consistent brand experience</strong>.  Consider the following points when you are making your product:

* Are there elements of your site for which there is a standard FT design expressed in an Origami component?  Search the [Origami registry](http://registry.origami.ft.com) to find out.  If so, you should be matching that style exactly, ideally by using the Origami component.  This includes things like [fonts](http://registry.origami.ft.com/components/o-fonts), [typography](http://registry.origami.ft.com/components/o-typography), [forms](http://registry.origami.ft.com/components/o-forms), [sharing buttons](http://registry.origami.ft.com/components/o-share), [galleries](http://registry.origami.ft.com/components/o-gallery) etc.
* Are you using standard brand assets like the FT logo, social media network icons, headshots of FT journalists or font files?  You must use the correct versions of these, available in the Registry. ([logos](http://registry.origami.ft.com/components/logo-images), [icons](http://registry.origami.ft.com/components/fticons).  In many cases it's a lot easier to retrieve these files though the [build service](https://www.ft.com/__origami/service/build/v2) or [image service](https://www.ft.com/__origami/service/image/v2), rather than hosting them within your product.  That way, you can be sure you're using the right one.


## Images

### Format and scale images correctly

There are a number of best practices to observe on the use of images:

* Make sure you use an appropriate format (typically JPEG for photographs, PNG for illustrations, SVG for icons - see below)
* Support screens with a high pixel density by creating images at a pixel density of 2, and trade off higher compression to achieve the same file size.  For example, if you want to display a 400x400 image, consider creating an 800x800 image and turning up the compression until the file size is acceptable.  Studies suggest the sharper but more compressed image works equally well on high and low density screens.
* Use an optimisation tool such as [ImageOptim](https://imageoptim.com/) to remove unnecessary metadata and colour profiles
* Scale image containers with viewport size so that images do not cause pages to scroll horizontally on small screens
* Use the Origami [responsive image service](https://www.ft.com/__origami/service/image/v2) to rescale your images dynamically based on the size of the container.

### Resolution independent SVGs, using the image service

The [responsive image service](https://www.ft.com/__origami/service/image/v2) helps serving resolution-independent SVG icons with a resized PNG fallback:

```scss
element {
	display: inline-block;
	width: 100px;
	height: 100px;

	// Older browsers: PNG fallback (resized to 100px wide)
	background-image: url('//www.ft.com/__origami/service/image/v2/images/raw/fticon:tick?width=100&format=png&source=my-product');

	// Modern browsers: SVG covering the whole size of the element
	// we declare multiple backgrounds so that only modern browsers read this property
	background-image: url('//www.ft.com/__origami/service/image/v2/images/raw/fticon:tick?format=svg&source=my-product'), none;
	background-size: cover;
}
```

## Performance

### Audit with PageSpeed

Use Google's [PageSpeed Insights](http://developers.google.com/speed/pagespeed/insights) service to analyse your page and try to score at least 80 on both the mobile and desktop categories.  This will cover off a wide variety of performance related best practices, including minification and concatenation of assets, image sizing and avoiding subresource requests that block rendering.

### Use the Polyfill Service

The [Origami Polyfill Service](http://polyfill.webservices.ft.com) creates custom bundles of polyfills based on the exact requirements of the user's browser, so we can avoid serving unnecessary code to browsers that already support a feature natively, but we can automatically upgrade browsers that don't support the feature.

This means you can assume modern web standards, you don't have to bundle any polyfills with your code, and performance on modern browsers is great - they get an empty file.

### Use responsive images

Don't download large images and then display them at a tiny size.  See [Images](#images).


## Security

### Don't collect data on insecure pages

When prompting the user for personal data such as email address, username, password or payment information, always serve the page with the form on it using HTTPS, and send the form submission to an HTTPS URL.

It's often considered OK to serve forms on insecure pages as long as the form posts to a secure destination.  This is not acceptable, because an attacker can modify the page that serves the form, to simply change the form post destination.  For more information, see [Steal my login](http://www.stealmylogin.com/).


## Accessibility

Not only is it a legal requirement to make reasonable adjustments to accommodate the needs of disabled readers, it also provides SEO benefits, and generally makes life easier for everyone.  Consider the following points:

* Don't disable zoom.  It should be possible for users to zoom the page using pinch gestures on touch screen devices
* Use ARIA to describe all labels, roles and states
* Always provide text alternatives to images or an empty `alt` if the image is not content


## HTML

### Trigger standards mode

Use an HTML5 DOCTYPE, and add the `X-UA-Compatible` meta tag to force Internet Explorer not to use compatibility mode.


	<!DOCTYPE html>
	<html>
	<head>
	   ...
	   <meta http-equiv="X-UA-Compatible" content="IE=edge" />


### Use UTF-8

Character encoding can cause problems, especially on sites that are predominantly in English with a few foreign characters here and there, where issues with character encoding can easily go unnoticed.

Ensure that your pages are **UTF-8** encoded, using both an HTTP response header **and** an HTML meta tag:


	<meta charset="UTF-8" />

Place this as the first tag within the `<head>` section of the page, before `<title>`, since it's important that the browser knows the right character set to use before it gets to any content.

### Use correct viewport sizing

By default, most mobile devices assume your site won't fit on a small screen so will pretend to be 900px wide and zoom out so that that fits on the screen.  Add the following viewport meta tag to the `<head>` of your page to make sure that the viewport is zoomed to 100% and the width is the same as the width of the device in both portrait and landscape orientation:


	<meta name="viewport" content="width=device-width, initial-scale=1.0" />

Do **not** specify a `user-scalable` rule.  The default is yes, so there's no need to include it, and the user should always be allowed to zoom if they want to.


### Use ARIA for state

ARIA is a set of accessibility standards that allow users of assistive technologies to get a better experience of the web.  It is important not only that you correctly flag when a state exists on an object, but that you choose the right state label, and you apply it using the right ARIA attribute.  Examples of states that you need to flag manually in your markup are **busy**, **selected** and **invalid**.


	<li aria-selected="true">I'm selected</li>
	<li aria-selected="false">I'm not selected, but am selectable</li>

Some states are automatically recognised by the browser and you don't need to do anything to enable them, such as **hovered** and **focused**, but it is possible to break the browser's behaviour (e.g. by setting a CSS `outline: none` property).  In the particular case of focus, please allow the browser to apply its default focus style if possible.

Some Origami components may not display correctly unless you apply the right state attributes.

* [Full list of states recognised by Origami]({{site.baseurl}}/docs/syntax/scss/#state)
* [ARIA states and properties specification](http://www.w3.org/TR/wai-aria/states_and_properties) (W3C)


### Validate your code

Run your fully assembled page through the [W3C validator](http://validator.w3.org/) to ensure you don't have any invalid code.  Origami components should have no invalid code, so if you find any please raise a bug.


## CSS

### Lint your code

Consider running [SASS Lint](https://github.com/sasstools/sass-lint) or CSSlint on your CSS. The syntax standards used by Origami are a good set of rules to use:

* [Origami Sass syntax standard]({{site.baseurl}}/docs/syntax/scss/#syntax-convention-rules)


### Minimise CSS specificity

CSS selectors have a hierarchy of specificity, which can lead to 'specificity wars', especially in products that are maintained by multiple non-collaborating teams.  Avoid these problems by following CSS best practices:

* Keep your selectors short: one single class token is ideal, and this is easily achievable with a convention such as BEM.
* Don't use IDs (you may need IDs in your HTML to connect together elements for accessibility, but you shouldn't ever need to reference them in CSS)
* Avoid using `!important` on anything except single property overrides.  Origami requires any use of `!important` to be justified in a comment, which is good practice to help developers who come after you.

* [Learn about BEM (block element modifier)](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)


### Measure lengths in pixels

All Origami components use pixels for length measures.  To avoid incompatibilities, and because relative measures such as ems result in over-complicated calculations, we recommend using pixels for lengths.


### Use standard colour palette

The FT standard colour palette is available as an Origami component, [o-colors](http://registry.origami.ft.com/components/o-colors).  Use use cases rather than palette colours, adding your own use cases where needed.  If you are using Sass, avoid including any raw colour values in your Sass, except as part of defining new colour use cases.

Information about how to use o-colors is available in the docs:

* [Learn about o-colors](http://registry.origami.ft.com/components/o-colors)


### Turn off hover effects when appropriate

If the user is on a touchscreen device and does not have a mouse or similar device connected, there will be no on-screen mouse cursor.  Hover effects are unnecessary in these situations, and can degrade the user experience on devices which [emulate hover](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariWebContent/HandlingEvents/HandlingEvents.html).

Equally, when a user is scrolling, hover effects can be unintentionally triggered by content moving *under* the mouse rather than the mouse moving *over* the content, which can have a [detrimental effect on scroll performance](http://www.html5rocks.com/en/tutorials/speed/unnecessary-paints/).

Consider using [o-hoverable](http://registry.origami.ft.com/components/o-hoverable), which intelligently flags when hover effects are desirable by toggling a class on the `<html>` element.


### Choose breakpoints based on content

Your styles should allow content to fit on screens down to around 300px wide.  In choosing breakpoints for style changes as the screen gets larger, you should not take any interest in the widths of devices in use.  It may be that turning a device from portrait to landscape view activates a different media query and displays the medium width layout.  Don't try to prevent this happening - it's a correct and inevitable result of well chosen breakpoints.


## JavaScript

### Lint your code

Consider running [ESLint](http://www.eslint.org/) over your JavaScript.  The syntax standards used by Origami are a good set of rules to use:

* [Origami JavaScript syntax standard]({{site.baseurl}}/docs/syntax/js/#syntax-convention-rules)


### Do not rely on JavaScript for navigation

Don't create links with `javascript:` prefixes in the href unless those links are not relevant to navigating the site.  Any UI element that appears to be navigation should have a real href.

Do not interfere with common browser affordances, for example, CMD+click or CTRL+click on a link usually opens the link in a new window, but capturing the click in JavaScript can break that feature, which will quite reasonably irritate users.
