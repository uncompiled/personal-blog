---
date:  2016-08-07
title: "Using Bootstrap with Accelerated Mobile Pages"
description: "This is an example of building AMP HTML pages from Bootstrap-based layouts."
tags: [ "accelerated mobile pages", "amp", "amphtml", "bootstrap", "css" ]
---

The other day, someone asked me how to use [Bootstrap](http://getbootstrap.com/)
with [Accelerated Mobile Pages](https://www.ampproject.org/).
My initial reaction was: "Don't use Bootstrap." However, it turns out that
there are a lot of [people using Bootstrap](http://trends.builtwith.com/docinfo/Twitter-Bootstrap).
Some of those users might want to use AMP HTML without rebuilding everything
from scratch.

Bootstrap makes it very convenient to build responsive, mobile-first websites.
By simply including Bootstrap's CSS, front-end developers gain access to an
extensive grid system and a library of reusable components, but that convenience
comes at a cost: 
[bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css)
adds **118k** to your critical path resources. On top of that, it's also likely that
your Bootstrap site doesn't use all of the included CSS classes.

# AMP CSS Restrictions

AMP makes some hard restrictions with CSS: [all CSS must be inline and size-bound](https://www.ampproject.org/docs/get_started/technical_overview.html#all-css-must-be-inline-and-size-bound). This is because CSS blocks the critical rendering path,
which means that the web browser will delay rendering content until the
[CSS Object Model](https://www.w3.org/TR/cssom-1/) is constructed.

- Inline CSS allows the CSSOM to be constructed on the initial
  request so the page can be rendered without waiting for an additional network
  call.  On current mobile networks,
  [latency (and not bandwidth)](https://www.igvita.com/2012/07/19/latency-the-new-web-performance-bottleneck/)
  has become the new performance bottleneck.  Mobile devices are constantly
  switching from high power to low power [Radio Resource Control](https://en.wikipedia.org/wiki/Radio_Resource_Control)
  states, which adds additional latency on top of standard internet routing latency.
- The size-bound ensures that front-end developers don't go
  crazy with hyper-specific CSS classes or copy/paste the entirety of
  `bootstrap.min.css` into the `<head>` tag.

Without these restrictions, it would be impossible for AMP to meet its performance goals.

# Cleaning Bootstrap's CSS

It's possible to use Bootstrap classes with AMP, but you'll have to remove the
unused CSS classes and inline the result in the `<style amp-custom>` tag.
It's a good practice to prune unused CSS, so this technique can be included in
any web development asset pipeline, including
[Rails](http://guides.rubyonrails.org/asset_pipeline.html) or
[Django](https://django-pipeline.readthedocs.io/en/latest/).

Since Node is popular these days, let's look at how to create this pipeline
using [Gulp](http://gulpjs.com/).

## Remove Unused CSS

[purifycss](https://github.com/purifycss/purifycss) helps us remove unused
CSS classes. It can be integrated into Gulp like this:

{{< highlight javascript >}}
var purify = require('gulp-purifycss');

gulp.task('purify', function() {
  return gulp.src(SOURCE.BOOTSTRAP_CSS)
    .pipe(purify([SOURCE.AMPHTML]))
    .pipe(gulp.dest('./amphtml/css'));
});
{{< /highlight >}}

## Minify and Insert CSS into AMP HTML

For this step, we use
[clean-css](https://github.com/jakubpawlowicz/clean-css) to minify the CSS
and then use
[gulp-html-replace](https://www.npmjs.com/package/gulp-html-replace)
to insert the result into the AMP HTML.

{{< highlight javascript >}}
var cleanCSS = require('gulp-clean-css');
var htmlreplace = require('gulp-html-replace');

gulp.task('inline-css', function() {
  return gulp.src(SOURCE.AMPHTML)
    .pipe(htmlreplace({
      'cssInline': {
        'src': gulp.src(SOURCE.CLEANED_CSS).pipe(cleanCSS()),
        'tpl': '<style amp-custom>%s</style>'
      }
    }))
    .pipe(gulp.dest(BUILD_PATH));
});
{{< /highlight >}}

## Validate the Output

Since AMP [disallows specific styles](https://www.ampproject.org/docs/guides/responsive/style_pages.html)
from being used, we can run the
[amphtml-validator](https://www.npmjs.com/package/amphtml-validator)
to find potential problems in the final output.

{{< highlight javascript >}}
var amphtmlValidator = require('amphtml-validator');
var fs = require('fs');

gulp.task('validate', function() {
  amphtmlValidator.getInstance().then(function (validator) {
    var input = fs.readFileSync(BUILD_PATH + '/index.html', 'utf8');
    var result = validator.validateString(input);
    // do something with validation result
  });
});
{{< /highlight >}}

Validation is an important part of the build process because we need to ensure
that our CSS creates the desired layout and styling while also adhering to the
AMP spec.

Running the amphtml-validator finds some invalid Bootstrap classes:

- CSS syntax error in tag 'style amp-custom' - saw invalid at rule '@-ms-viewport'
- The text (CDATA) inside tag 'style amp-custom' contains 'CSS !important', which is disallowed

After removing these classes from the source CSS, we have valid AMP HTML.

# Results

Once the unused (and invalid) classes are stripped from the original Bootstrap
CSS, the minified output shrinks from 120kb down to 12kb.

## Before

<amp-img src="https://raw.githubusercontent.com/uncompiled/amp-bootstrap/master/screenshots/html.jpg" 
layout="responsive"
alt="Original Bootstrap HTML" width="1428" height="822"></amp-img>

## After

<amp-img src="https://raw.githubusercontent.com/uncompiled/amp-bootstrap/master/screenshots/amp.jpg" 
layout="responsive"
alt="AMP Bootstrap HTML" width="1428" height="822"></amp-img>

The pages look exactly the same and now you have both valid AMP HTML and cleaner CSS.

**For reference, here's the [source code](https://github.com/uncompiled/amp-bootstrap) for this example.**