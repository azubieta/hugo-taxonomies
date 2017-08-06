# Hugo taxonomies example

Live view: [http://hugo-taxonomies.netlify.com/](http://hugo-taxonomies.netlify.com/)

## The problem

I wanted to add custom metadata to taxonomies. There is not a lot of documentation on how to do this. So I came up with this technique.

## Solution

Let's say I have a shoes website. My main content would be product pages with the metadata for the product. My main taxonomy would be brand, in order to be able to assign a brand to each product.

in config.toml

```
[taxonomies]
  brand = "brands"
```

This is how a product page would look like:

```
---
title: "Adidas Ligra 5"
description: "This is the description for Adidas Ligra 5"
brands:
  - Adidas
date: 2017-08-05T07:43:13-06:00
draft: false
---
```

Now, when I visit `/brands/` I want a to display all my brands, but also give a little more information about each one, like a logo, a description and the brand's official website link. To store the information for each brand, I create a page for each one. For example:

`/content/brands/nike/_index.md`

```
---
title: "Nike"
description: "This is the description for Nike"
website: "http://nike.com"
logo: "/images/nike.png"
---
```

**Attention:** The folder structure is important: `/content/<taxonomy>/<term>/_index.md`

Then all I need to do is list these pages in my taxonomyTerms template (`/layouts/default/terms.html`). But grouping those pages can be tricky and confusing. What you want is to be able to treat `/brands` almost like a section. But since the pages are nested, you can't really do it. So here's my little trick. I added a `taxonomy_indexes: true` parameter in the brand pages.

```
---
title: "Nike"
description: "This is the description for Nike"
website: "http://nike.com"
logo: "/images/nike.png"
taxonomy_indexes: true
---
```

Finally, I just list them in a breeze like this on my taxonomyTerms template (`/layouts/_default/terms.html`):

```
<ul>
  {{ range ( where .Data.Pages ".Params.taxonomy_indexes" "==" true) }}
    <li>
      <h3>{{ .Title }}</h3>
      <dl>
        <dt>Link to the brand page</dt><dd><a href="{{ .Permalink }}">{{ .Permalink }}</a></dd>
        <dt>Website</dt><dd>{{ .Params.website }}</dd>
        <dt>Description</dt><dd>{{ .Params.description }}</dd>
        <dt>Logo</dt><dd><img src="{{ .Params.logo }}" height="90" /></dd>
      </dl>
    </li>
  {{ end }}
</ul>
```

Finally, in my product page template, `/layouts/_default.html`, I listed other products that belong to the same brand. To do that, it is necessary to filter pages dynamically. I did it like so:

```
{{ range where .Site.Pages ".Permalink" "!=" .Permalink }}
  {{ if eq .Params.brands $.Params.brands }}
    <li>
      <a href="{{ .Permalink}}">{{ .Title }}</a>
    </li>
  {{ end }}
{{ end }}
```

As you can see, I ranged through all the pages except the current page and then filtered only the pages where the brands match the current product's brands. That bit works perfectly, but I think it could be improved.


That's it, if you follow those steps you will be able to add custom metadata to your taxonomies with little effort.
