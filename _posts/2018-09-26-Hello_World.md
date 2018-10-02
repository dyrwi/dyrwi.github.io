---
title:  "Hello World - Syntax Highlighting"
excerpt: "Post displaying the various ways of highlighting code in Markdown."
author: Ben Wilson
classes: wide
toc: true
toc_label: "My contents"
toc_icon: "cog"
---

# Welcome

**Hello world**, this is my first Jekyll blog post.

I hope you like it! Wow I have now changed it

### Jekyll Highlight Tag

An example of a code blocking using Jekyll's [`{% raw %}{% highlight %}{% endraw %}` tag](https://jekyllrb.com/docs/templates/#code-snippet-highlighting).

{% highlight javascript linenos %}
// 'gulp html' -- does nothing
// 'gulp html --prod' -- minifies and gzips HTML files for production
gulp.task('html', () => {
  return gulp.src(paths.siteFolderName + paths.htmlPattern)
    .pipe(when(argv.prod, htmlmin({
      removeComments: true,
      collapseWhitespace: true,
      collapseBooleanAttributes: false,
      removeAttributeQuotes: false,
      removeRedundantAttributes: false,
      minifyJS: true,
      minifyCSS: true
    })))
    .pipe(when(argv.prod, size({title: 'optimized HTML'})))
    .pipe(when(argv.prod, gulp.dest(paths.siteFolderName)))
    .pipe(when(argv.prod, gzip({append: true})))
    .pipe(when(argv.prod, size({
      title: 'gzipped HTML',
      gzip: true
    })))
    .pipe(when(argv.prod, gulp.dest(paths.siteFolderName)))
});
{% endhighlight %}

Wow
