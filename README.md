# Shopify-Debut-theme-tweaks

<ins>TO SHOW A DISCOUNT 'BANNER' ONLY ON SPECIFIC PRODUCT PAGES</ins>

Add the following code to product-template.liquid
```
{% for collection in product.collections %}
   {% if collection.title == 'YOUR COLLECTION NAME' %}
      <div class="collection-banner-container">
         <span class="collection-banner">YOUR TEXT</span>
      </div>
   {% endif %}
{% endfor %}
```
---
<ins>TO CONTROL THE NUMBER OF COLUMNS USED IN DROPDOWN MENU FOR A SPECIFIC NAVLABEL</ins>

Add the following code to product-template.liquid
```
/*================ Make Fishing menu multi-column (Jon) ================*/
#SiteNavLabel-fishing { width: auto; }
#SiteNavLabel-fishing  ul { column-count: 4; }
#SiteNavLabel-fishing  ul li {-webkit-column-break-inside: avoid;page-break-inside: avoid;    break-inside: avoid; }
```
---
<ins>TO ADD BREADCRUMBS</ins>

Follow instructions here https://help.shopify.com/en/themes/customization/navigation/add-breadcrumbs
Also worth looking at this https://www.shopify.co.uk/partners/blog/breadcrumb-navigation

But slightly amend the code to correct positioning to align with page-width by using ```<div class="page-width">```

```
{% unless template == 'index' or template == 'cart' or template == 'list-collections' %}
<nav class="breadcrumb" role="navigation" aria-label="breadcrumbs">
  <div class="page-width">
  <a href="/" title="Home">Home</a>
  {% if template contains 'page' %}
    <span aria-hidden="true">&rsaquo;</span>
    <span>{{ page.title }}</span>
  {% elsif template contains 'product' %}
    {% if collection.url %}
      <span aria-hidden="true">&rsaquo;</span>
      {{ collection.title | link_to: collection.url }}
    {% endif %}
    <span aria-hidden="true">&rsaquo;</span>
    <span>{{ product.title }}</span>
  {% elsif template contains 'collection' and collection.handle %}
    <span aria-hidden="true">&rsaquo;</span>
    {% if current_tags %}
      {% capture url %}/collections/{{ collection.handle }}{% endcapture %}
      {{ collection.title | link_to: url }}
      <span aria-hidden="true">&rsaquo;</span>
      <span>{{ current_tags | join: " + " }}</span>
    {% else %}
      <span>{{ collection.title }}</span>
    {% endif %}
  {% elsif template == 'blog' %}
    <span aria-hidden="true">&rsaquo;</span>
    {% if current_tags %}
      {{ blog.title | link_to: blog.url }}
      <span aria-hidden="true">&rsaquo;</span>
      <span>{{ current_tags | join: " + " }}</span>
    {% else %}
    <span>{{ blog.title }}</span>
    {% endif %}
  {% elsif template == 'article' %}
    <span aria-hidden="true">&rsaquo;</span>
    {{ blog.title | link_to: blog.url }}
    <span aria-hidden="true">&rsaquo;</span>
    <span>{{ article.title }}</span>
  {% else %}
   <span aria-hidden="true">&rsaquo;</span>
   <span>{{ page_title }}</span>
  {% endif %}
</nav>
{% endunless %}
```

Also add the following CSS to theme.scss.liquid
```
.breadcrumb {
 padding-top: 2px;
 font-size: small;
}

.breadcrumb {
  color: inherit;
  font-weight: normal;
  text-decoration: none;
}

.breadcrumb :hover,
.breadcrumb focus {
  text-decoration: underline;
}
```
More here re Structured Data/SEO for Breadcrumbs

https://www.shopify.co.uk/partners/blog/breadcrumb-navigation
https://gist.github.com/mirceapiturca/9be3186b607922e0a1b9#file-shopify-breadcrumb-liquid-rich-snippets
https://www.rockpapercopy.com/breadcrumbs-seo/

---
<INS> HOW TO ADD TABS TO PRODUCT DESCRIPTIONS </INS>

See this tutorial https://community.shopify.com/c/Shopify-Design/Adding-tabs-on-product-page-simple-entry/td-p/430363

---
<INS> HOW TO ADD EXTENDED PRODUCT DESCRIPTION INFO BENEATH PRODUCT IMAGES </INS>

Add code below in 'product-template.liquid' file, find 'product-single__description rte' and replace.

```
<div class="product-single__description rte" id="first_product_description">
      {{ product.description | split: '<!-- split -->' | first }}
          </div>
       </div>
        <div class="product-single__description rte" id="last_product_description">
      {{ product.description | split: '<!-- split -->' | last }}
      </div>
 ```
 
 To also apply to featured product, amend to the following in 'featured-product.liquid'
 
 ```
 <div class="product-single__description rte" id="first_product_description">
          {% unless product == empty %}
            {{ product.description | split: '<!-- split -->' | first }}
          </div>
       </div>
      </div>
      
        <div class="product-single__description rte" id="last_product_description">
      {{ product.description | split: '<!-- split -->' | last }}
      </div>
          {% else %}
            {{ 'homepage.onboarding.product_description' | t }}
          {% endunless %}
        </div>
```

If product descriptions don't all contain ```<!-- split -->``` then need to wrap in an IF statement. Makes it so that descriptions without ```<!-- split -->``` display as original theme code.

```
      <div class="product-single__description rte" id="first_product_description">
      {{ product.description | split: '<!-- split -->' | first }}
          </div>
      </div>
    </div>
    {% if product.description contains "<!-- split -->" %}
        <div class="product-single__description rte" id="last_product_description">
      {{ product.description | split: '<!-- split -->' | last }}
     &nbsp;
      </div>
  		{% endif %}
```

---
<INS> REMOVE POWERED BY SHOPIFY & ADD CUSTOM TEXT </INS>

```
<small class="site-footer__copyright-content">&copy; {{ 'now' | date: "%Y" }}, {{ shop.name | link_to: '/' }} Ltd. Trading since 2003. VAT No. 808782009</small>
        <small class="site-footer__copyright-content site-footer__copyright-content--powered-by">{{ powered_by_link }}</small>
```
---
<INS> REMOVE TAXES CALCULATED AT CHECKOUT FROM PRODUCT PAGE </INS>

See this article https://community.shopify.com/c/Shopify-Design/Delete-Shipping-calculated-at-checkout/td-p/533355

---
<ins> SHOW DISCOUNT % </ins>

See this article https://www.blackbeltcommerce.com/shopify-how-to-show-percentage-discount-saved/

---
<ins>ADD 'From' IN FRONT OF LOWEST PRICE IF VARIANTS HAVE DIFFERENT PRICES AND ONLY SHOW ON Collections PAGE</ins>

**Note that this deliberately does not add 'From' if products are on Sale**

Amend code in product-price.liquid

**From this:**
```
<div class="price__regular">
    <dt>
      <span class="visually-hidden visually-hidden--inline">{{ 'products.product.regular_price' | t }}</span>
    </dt>
    <dd>
      <span class="price-item price-item--regular" data-regular-price>
       {{ money_price }}
      </span>
    </dd>
  </div>
```


**To this:**
```
<!-- Start code to add 'From' price' -->
 {% if product.price_max != product.price_min and template == 'collection' %}
  <div class="price__regular">
    <dt>
      <span class="visually-hidden visually-hidden--inline">{{ 'products.product.regular_price' | t }}</span>
    </dt>
    <dd>
      <span class="price-item price-item--regular" data-regular-price>
       From {{product.price_min | money}}
      </span>
    </dd>
  </div>
  {% else %}
  <div class="price__regular">
    <dt>
      <span class="visually-hidden visually-hidden--inline">{{ 'products.product.regular_price' | t }}</span>
    </dt>
    <dd>
      <span class="price-item price-item--regular" data-regular-price>
       {{ money_price }}
      </span>
    </dd>
  </div>
  {% endif %}
  <!-- End code to add 'From' price' -->
```
If you want to show From and To price i.e. lowest priced variant to highest priced variant, amend code to look like this:

```
<!-- Start code to add 'From' and 'To' price' -->
 {% if product.price_max != product.price_min and template == 'collection' %}
  <div class="price__regular">
    <dt>
      <span class="visually-hidden visually-hidden--inline">{{ 'products.product.regular_price' | t }}</span>
    </dt>
    <dd>
      <span class="price-item price-item--regular" data-regular-price>
      <!-- This is the amended line -->
       From {{ product.price_min | money }} To {{ product.price_max | money }}
      </span>
    </dd>
  </div>
  {% else %}
  <div class="price__regular">
    <dt>
      <span class="visually-hidden visually-hidden--inline">{{ 'products.product.regular_price' | t }}</span>
    </dt>
    <dd>
      <span class="price-item price-item--regular" data-regular-price>
       {{ money_price }}
      </span>
    </dd>
  </div>
  {% endif %}
  <!-- End code to add 'From' and 'To' price' -->
```

---


<ins> Hide unavailable variants </ins>

See this article https://community.shopify.com/c/Shopify-Design/How-to-hide-irrelevant-variant-in-Debut-theme/td-p/360226/highlight/true

---

<INS> TO CHANGE COLLECTIONS SECTION ON HOMEPAGE TO SHOW BUTTONS RATHER THAN JUST TEXT </INS>

Add this line of code ```<div class="btn collections_btn">``` to the section below in 'collection-grid-item.liquid'

```
<div class="collection-grid-item__title-wrapper">
      <div class="collection-grid-item__title h3">
         <div class="btn collections_btn">
        {% if collection.title == blank %}
          {{ 'homepage.onboarding.collection_title' | t }}
        {% else %}
          {{ collection.title }}
        {% endif %}
      </div>
    </div>
```

Style thus:

```
.collections_btn {
  text-align: center;
  width: 100%;
  opacity: 0.85;
  font-size: 0.6em;
}

.collection-grid-item:hover {
  webkit-transform: scale(1.025);
  transform: scale(1.025);
  transition: transform .8s;
}
```

---

<INS> CREATE A HTML SITEMAP (WHILST SHOPIFY AUTOMATICALLY CREATES AN XML SITEMAP, SOME PEOPLE ALSO WANT A HTML ONE) </INS>

Create a new template based on a Page, I have called mine 'htmlsitemap'. Code in that template will look like this:

```
<div class="page-width">
  <div class="grid">
    <div class="grid__item medium-up--five-sixths medium-up--push-one-twelfth">
      <div class="section-header text-center">
        <h1>{{ page.title }}</h1>
      </div>

      <div class="rte">
        {{ page.content }}
      </div>
    </div>
  </div>
</div>
```

Replace ```{{ page.content }}``` with the following code:

```
{%- comment -%} Jon start of HTML SiteMap code {%- endcomment -%}
        <div class="row">
<div class="col-md-6">
  <h2>Collections</h2>
       <ul>
  {% for c in collections %}
  <li>
    <a href="{{ c.url }}">{{ c.title }}</a>
  </li>
  {% endfor %}
</ul> 
        </div>
          <div class="col-md-6">
            <h2>Products</h2>
            <ul>
              
{% paginate collections.all.products by 1000 %}
  {% for product in collections.all.products %}
  	<li>
    <a href="{{ product.url }}">{{ product.title }}</a>
 	 </li>
  {% endfor %}
{% endpaginate %}

            </ul> 
          </div>
    </div>
    {%- comment -%} Jon end of HTML SiteMap code {%- endcomment -%}   

```

Then go to Online Store>Pages>Add Page and create a new page making sure to select 'page.htmlsitemap' as the template

---

<INS> To add copy to product pages for a specific Vendor </INS>

Go to 'product-template.liquid' and add the following code beneath ```<div class="product-single__description rte">```

```
{%- comment -%}
          Jon custom wording for all 'VendorName' products - to appear above product descritption
        {%- endcomment -%}
          {% if product.vendor == 'VendorName' %}
          <p class=generic-product-copy> Add your copy here.</p>
{% endif %}
```
---
